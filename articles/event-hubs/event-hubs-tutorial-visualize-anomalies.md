---
title: Azure Event Hubs – Adatanomáliák vizualizációja valós idejű eseményekben
description: 'Oktatóanyag: Az adatanomáliák vizualizációja a Microsoft Azure Event Hubs'
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: bb7d8da2498005b8b2e1183a836d9385f3d31e5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783736"
---
# <a name="tutorial-visualize-data-anomalies-in-real-time-events-sent-to-azure-event-hubs"></a>Oktatóanyag – Az Azure Event Hubsba küldött valós idejű események adatanomáliáinak vizualizációja

Az Azure Event Hubsban az Azure Stream Analytics használatával ellenőrizheti a bejövő adatokat, és azonosíthatja az anomáliákat, amelyeket ezután megjeleníthet a Power BI-ban. Tegyük fel, hogy van több ezer eszköze, amelyek folyamatosan valós idejű adatokat küldenek egy eseményközpontba. Ez több millió eseményt jelent másodpercenként. Hogyan lehet anomáliákat vagy hibákat keresni ennyi adatban? Mi történik például, ha az eszközök hitelkártya-tranzakciókat küldenek, és bárhol rögzítenie kell, ahol több tranzakció történik több országban/régióban egy 5 másodperces időintervallumon belül? Ez akkor történhet, ha valaki hitelkártyákat lop, majd a világ különböző pontjain több dolgot vásárol velük egyszerre. 

Ebben az oktatóanyagban ezt a példát szimulálja. Futtatni fog egy alkalmazást, amely hitelkártya-tranzakciókat hoz létre, és elküldi őket egy eseményközpontba. Ezután valós időben beolvassa az adatstreamet az Azure Stream Analytics használatával, amely elkülöníti az érvényes tranzakciókat az érvénytelen tranzakcióktól, majd az Power BI használatával vizuálisan azonosítja az érvénytelenként megjelölt tranzakciókat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * Eseményközpont létrehozása
> * A hitelkártya-tranzakciókat küldő alkalmazás futtatása
> * Stream Analytics-feladat konfigurálása a tranzakciók feldolgozására
> * Power BI-vizualizáció konfigurálása az eredmények megjelenítésére

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot][], mielőtt hozzákezd.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- A [Visual Studio](https://www.visualstudio.com/) telepítése. 
- A Stream Analytics-feladat kimenetének elemzéséhez szüksége lesz egy Power BI-fiókra. A [Power BI-t ingyenesen kipróbálhatja](https://app.powerbi.com/signupredirect?pbi_source=web).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-resources"></a>Erőforrások beállítása

Ehhez az oktatóanyaghoz szüksége lesz egy Event Hubs-névtérre és egy eseményközpontra. Ezeket az erőforrásokat az Azure CLI vagy az Azure PowerShell használatával hozhatja létre. Ugyanazt az erőforráscsoportot és helyet használja minden erőforráshoz. Így mindent egyetlen lépésben távolíthat el az erőforráscsoport törlésével.

Az alábbi szakaszok ismertetik a szükséges lépések elvégzésének módját. Kövesse a parancssori felületre *vagy* a PowerShellre vonatkozó utasításokat a következő lépések végrehajtásához:

1. Hozzon létre [egy erőforráscsoportot.](../azure-resource-manager/management/overview.md) 

2. Event Hubs-névtér létrehozása. 

3. Eseményközpont létrehozása.

> [!NOTE]
> Minden szkript tartalmaz olyan beállított változókat, amelyekre az oktatóanyag későbbi részében szükség lesz. Ezek közé tartozik az erőforráscsoport neve ($resourceGroup), az Event Hubs-névtér (**$eventHubNamespace**) és az eseményközpont neve (**$eventHubName**). A cikk későbbi részében ezek dollárjel ($) előtaggal fognak szerepelni, így tudni fogja, hogy a szkriptben lettek beállítva.

<!-- some day they will approve the tab control; 
  When that happens, put CLI and PSH in tabs. -->

### <a name="set-up-your-resources-using-azure-cli"></a>Erőforrások beállítása az Azure CLI használatával

Másolja és illessze be az alábbi szkriptet a Cloud Shellbe. A szolgáltatás azt feltételezi, hogy már bejelentkezett, és soronként futtatja a szkriptet.

Azokhoz a változókhoz, amelyeknek globálisan egyedinek kell lenniük, a következő van hozzáfűzve: `$RANDOM`. Ha a szkript fut, és a változók be vannak állítva, a rendszer létrehoz egy véletlenszerű numerikus sztringet, és hozzáfűzi a rögzített sztring végéhez, hogy a sztring egyedi legyen.

```azurecli-interactive
# Set the values for location and resource group name.
location=westus
resourceGroup=ContosoResourcesEH

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
eventHubNamespace=ContosoEHNamespace$RANDOM
echo "Event Hub Namespace = " $eventHubNamespace

# Create the Event Hubs namespace.
az eventhubs namespace create --resource-group $resourceGroup \
   --name $eventHubNamespace \
   --location $location \
   --sku Standard

# The event hub name must be globally unique, so add a random number to the end.
eventHubName=ContosoEHhub$RANDOM
echo "event hub name = " $eventHubName

# Create the event hub.
az eventhubs eventhub create --resource-group $resourceGroup \
    --namespace-name $eventHubNamespace \
    --name $eventHubName \
    --message-retention 3 \
    --partition-count 2

# Get the connection string that authenticates the app with the Event Hubs service.
connectionString=$(az eventhubs namespace authorization-rule keys list \
   --resource-group $resourceGroup \
   --namespace-name $eventHubNamespace \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString \
   --output tsv)
echo "Connection string = " $connectionString 
```

### <a name="set-up-your-resources-using-azure-powershell"></a>Erőforrások beállítása az Azure PowerShell használatával

Másolja és illessze be az alábbi szkriptet a Cloud Shellbe. A szolgáltatás azt feltételezi, hogy már bejelentkezett, és soronként futtatja a szkriptet.

Azokhoz a változókhoz, amelyeknek globálisan egyedinek kell lenniük, a következő van hozzáfűzve: `$(Get-Random)`. Ha a szkript fut, és a változók be vannak állítva, a rendszer létrehoz egy véletlenszerű numerikus sztringet, és hozzáfűzi a rögzített sztring végéhez, hogy a sztring egyedi legyen.

```azurepowershell-interactive
# Log in to Azure account.
Login-AzAccount

# Set the values for the location and resource group.
$location = "West US"
$resourceGroup = "ContosoResourcesEH"

# Create the resource group to be used  
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
$eventHubNamespace = "contosoEHNamespace$(Get-Random)"
Write-Host "Event Hub Namespace is " $eventHubNamespace

# The event hub name must be globally unique, so add a random number to the end.
$eventHubName = "contosoEHhub$(Get-Random)"
Write-Host "Event hub Name is " $eventHubName

# Create the Event Hubs namespace.
New-AzEventHubNamespace -ResourceGroupName $resourceGroup `
     -NamespaceName $eventHubNamespace `
     -Location $location

# Create the event hub.
$yourEventHub = New-AzEventHub -ResourceGroupName $resourceGroup `
    -NamespaceName $eventHubNamespace `
    -Name $eventHubName `
    -MessageRetentionInDays 3 `
    -PartitionCount 2

# Get the event hub key, and retrieve the connection string from that object.
# You need this to run the app that sends test messages to the event hub.
$eventHubKey = Get-AzEventHubKey -ResourceGroupName $resourceGroup `
    -Namespace $eventHubNamespace `
    -AuthorizationRuleName RootManageSharedAccessKey

# Save this value somewhere local for later use.
Write-Host "Connection string is " $eventHubKey.PrimaryConnectionString
```

## <a name="run-app-to-produce-test-event-data"></a>Alkalmazás futtatása a teszt-eseményadatok létrehozásához

A Event Hubs [GitHub-minták tartalmaznak](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet) egy anomáliadetektor alkalmazást, amely tesztadatokat állít elő Az Ön számára. Az alkalmazás szimulálja a hitelkártyák használatát azáltal, hogy hitelkártya-tranzakciókat ír az eseményközpontba, és időnként több tranzakciót ír ugyanahhoz a hitelkártyához, több helyszínen, hogy a rendszer anomáliaként jelölje meg őket. Az alkalmazás futtatásához kövesse az alábbi lépéseket: 

1. Töltse le az [Azure Event Hubs-mintákat](https://github.com/Azure/azure-event-hubs/archive/master.zip) a GitHubról, majd bontsa ki őket helyben.
2. Lépjen az **\azure-event-hubs-master\samples\DotNet \\ mappába.** 
3. Váltson az **Azure.Messaging.EventHubs\AnomalyDetector \\** mappára, és kattintson duplán az **AnomalyDetector.sln** fájlra a megoldás Visual Studio. 

    A régi Microsoft.Azure.EventHubs csomagot használó minta régi verziójának használatára nyissa meg a megoldást a **Microsoft.Azure.EventHubs\AnomalyDetector mappából.** 
3. Nyissa meg a Program.cs fájlt, és cserélje le az **Event Hubs kapcsolati sztringjét** a szkript futtatásakor mentett kapcsolati sztringre. 
4. Cserélje le az **Eseményközpont neve** elemet az eseményközpontja nevére. Az alkalmazás futtatásához nyomja le az F5 billentyűt. Az alkalmazás elkezd eseményeket küldeni az eseményközpontba, és addig folytatja, amíg 1000 eseményt el nem küldött. Néhány esetben az alkalmazásnak futnia kell ahhoz, hogy le lehessen kérni az adatokat. Az alábbi utasítások szükség esetén fel fogják hívni a figyelmet ezekre az esetekre.

## <a name="set-up-azure-stream-analytics"></a>Az Azure Stream Analytics beállítása

Most már adatokat streamelhet az eseményközpontba. Az adatok Power BI-vizualizációban történő felhasználásához először állítson be egy Stream Analytics-feladatot az adatok lekérésére, amelyek ezután bekerülnek a Power-BI-vizualizációba.

### <a name="create-the-stream-analytics-job"></a>A Stream Analytics-feladat létrehozása

1. Az Azure Portalon kattintson az **Erőforrás létrehozása** gombra. Írja be a **stream analytics** kifejezést a keresőmezőbe, majd nyomja le az **Enter** billentyűt. Válassza a **Stream Analytics-feladat** lehetőséget. Kattintson a Stream Analytics-feladat panel **Létrehozás** elemére. 

2. Adja meg a feladat alábbi adatait:

   **Feladat neve**: Használja a **contosoEHjob** nevet. Ez a mező a feladat nevét tartalmazza, amelynek globálisan egyedinek kell lennie.

   **Előfizetés**: Válassza ki előfizetését.

   **Erőforráscsoport**: Használja az eseményközpont által használt erőforráscsoportot (**ContosoResourcesEH**).

   **Hely**: Használja a beállítási szkriptnél használt helyet (**USA nyugati régiója**).

   ![Új Azure Stream Analytics-feladat létrehozását bemutató képernyőkép.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-add-job.png)

    A többi mezőnél fogadja el az alapértelmezett beállításokat. Kattintson a **Létrehozás** lehetőségre. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

Ha nem a Portal **Stream Analytics-feladat** panelén tartózkodik, visszatérhet a Stream Analytics-feladatához, ha a Portalon az **Erőforráscsoportok** elemre kattint, majd kiválasztja az erőforráscsoportját (**ContosoResourcesEH**). Ez a művelet megjeleníti a csoportban található összes erőforrást, így ki tudja választani a Stream Analytics-feladatát. 

A Steam Analytics-feladat bemenetei az eseményközpontból származó hitelkártya-tranzakciók.

> [!NOTE]
> A dollárjellel ($) kezdődő változók értékei az indítási szkriptekben lettek beállítva az előző szakaszokban. Itt ugyanazokat az értékeket kell használnia az Event Hubs-névtér és az eseményközpont neve mezők megadásakor.

1. A **Feladattopológia** területen kattintson a **Bemenetek** elemre.

2. A **Bemenetek** panelen kattintson a **Streambemenet hozzáadása** elemre, és válassza az Event Hubsot. A megjelenő képernyőn töltse ki az alábbi mezőket:

   **Bemeneti alias**: Használja a **contosoinputs** aliast. Ez a mező a bemeneti stream nevét tartalmazza, amelyet az adatlekérdezés meghatározásakor kell használni.

   **Előfizetés**: Válassza ki előfizetését.

   **Event Hubs-névtér**: Válassza ki az Event Hubs-névterét ($**eventHubNamespace**). 

   **Eseményközpont neve**: Kattintson a **Meglévő használata** elemre, és válassza ki az eseményközpontját ($**eventHubName**).

   **Eseményközpont szabályzatának neve**: Válassza a **RootManageSharedAccessKey** lehetőséget.

   **Eseményközpont fogyasztói csoportja**: Hagyja üresen ezt a mezőt az alapértelmezett fogyasztói csoport használatához.

   A többi mezőnél fogadja el az alapértelmezett beállításokat.

   ![Bemeneti stream Stream Analytics-feladathoz való hozzáadását bemutató képernyőkép.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-inputs.png)

5. Kattintson a **Mentés** gombra.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia** területen kattintson a **Kimenetek** elemre. Ez a mező a kimeneti stream nevét tartalmazza, amelyet az adatlekérdezés meghatározásakor kell használnia.

2. A **Kimenetek** panelen kattintson a **Hozzáadás**, majd a **Power BI** elemre. A megjelenő képernyőn töltse ki az alábbi mezőket:

   **Kimeneti alias**: Használja a **contosooutputs** aliast. Ez a mező a kimenet egyedi aliasát tartalmazza. 

   **Adatkészlet neve**: Használja a **contosoehdataset** nevet. Ez a mező a Power BI-ban használni kívánt adatkészlet nevét tartalmazza. 

   **Tábla neve**: Használja a **contosoehtable** nevet. Ez a mező a Power BI-ban használni kívánt tábla nevét tartalmazza. 

   A többi mezőnél fogadja el az alapértelmezett beállításokat.

   ![A Stream Analytics-feladat kimenetének beállítását bemutató képernyőkép.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-outputs.png)

3. Kattintson az **Engedélyezés** gombra, és jelentkezzen be a Power BI-fiókjába.

4. A többi mezőnél fogadja el az alapértelmezett beállításokat.

5. Kattintson a **Mentés** gombra.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének konfigurálása

Ez a lekérdezés a Power BI-vizualizációnak küldött végső adatok lekérésére szolgál. A **contosoinputs** és a **contosooutputs** aliasokat használja, amelyeket korábban, a feladat beállításakor adott meg. Ez a lekérdezés a csalárdnak ítélt tranzakciókat kéri le, azaz olyan hitelkártya-tranzakciókat, amelyekben egy hitelkártyaszámmal több tranzakciót végeztek különböző helyszíneken, egy adott öt másodperces időintervallumban.

1. A **Feladattopológia** területen kattintson a **Lekérdezés** elemre.

2. Cserélje le a lekérdezést a következőre: 

   ```SQL
   /* criteria for fraud:
      credit card purchases with the same card
      in different locations within 5 seconds
   */
   SELECT System.Timestamp AS WindowEnd, 
     COUNT(*) as FraudulentUses      
   INTO contosooutputs
   FROM contosoinputs CS1 TIMESTAMP BY [Timestamp]
       JOIN contosoinputs CS2 TIMESTAMP BY [Timestamp]
       /* where the credit card # is the same */
       ON CS1.CreditCardId = CS2.CreditCardId
       /* and time between the two is between 0 and 5 seconds */
       AND DATEDIFF(second, CS1, CS2) BETWEEN 0 AND 5
       /* where the location is different */
   WHERE CS1.Location != CS2.Location
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. Kattintson a **Mentés** gombra.

### <a name="test-the-query-for-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének tesztelése 

1. Futtassa az anomáliaérzékelő alkalmazást, hogy adatokat küldjön az eseményközpontba, miközben beállítja és futtatja a tesztet. 

2. A Lekérdezés panelen kattintson a **contosoinputs** bemenet melletti pontokra, majd válassza a **Mintaadatok bemenetből** lehetőséget.

3. Adja meg, hogy három percnyi adatot szeretne, majd kattintson az **OK** gombra. Várjon, amíg a rendszer értesíti arról, hogy az adatok mintavételezése befejeződött.

4. Kattintson a **Tesztelés** elemre, és ellenőrizze, hogy megkapja-e az eredményeket. Az eredmények az alsó panel **Eredmények** szakaszában jelennek meg, közvetlenül a lekérdezés alatt.

5. Zárja be a Lekérdezés panelt.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

A Stream Analytics-feladat területen kattintson az **Indítás**, a **Most**, majd az **Indítás** elemre. Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

## <a name="set-up-the-power-bi-visualizations"></a>A Power BI-vizualizációk beállítása

1. Futtassa az Anomáliaérzékelő alkalmazást, hogy adatokat küldjön az eseményközpontba, miközben beállítja a Power BI-vizualizációt. Előfordulhat, hogy többször is futtatnia kell, mivel egy alkalommal csak 1000 tranzakciót hoz létre.

2. Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/)-fiókjába.

3. Lépjen a **Saját munkaterületre**.

4. Kattintson az **Adatkészletek** elemre.

   Megjelenik az adatkészlet, amelyet a Stream Analytics-feladat kimenetének létrehozásakor adott meg (**contosoehdataset**). Akár 5-10 percet is igénybe vehet, mire az adatkészlet először megjelenik.

5. Kattintson az **Irányítópultok**, majd a **Létrehozás** gombra, és válassza az **Irányítópult** elemet.

   ![Az Irányítópultok és a Létrehozás gombok képernyőképe.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-dashboard.png)

6. Adja meg az irányítópult nevét, majd kattintson a **Létrehozás** elemre. Használja a **Hitelkártya-anomáliák** nevet.

   ![Képernyőkép az irányítópult nevének megadásáról.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-name.png)

7. Az Irányítópult lapon kattintson a **Csempe** hozzáadása elemre, válassza az Egyéni **streamelési** adatok lehetőséget a **VALÓS IDEJŰ ADATOK szakaszban,** majd kattintson a Tovább **gombra.**

   ![Képernyőkép a csempe forrásának megadásáról.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-real-time-data.png)

8. Válassza ki az adatkészletét (**contosoehdataset**), és kattintson a **Tovább** gombra.

   ![Képernyőkép az adatkészlet megadásáról.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-select-dataset.png)

9. A vizualizáció típusánál válassza a **Kártya** lehetőséget. A **Mezők területen** kattintson az Érték **hozzáadása** elemre, majd válassza a `fraudulentuses` lehetőséget.

   ![Képernyőkép a vizualizációtípus és a mezők megadásáról.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-tile.png)

   Kattintson a **Tovább** gombra.

10. A címhez írja be a **Csalárd felhasználás**, az alcímhez pedig az **Összeg az elmúlt néhány percben** szöveget. Kattintson az **Alkalmaz** gombra. Ez menti a csempét az irányítópultra.

    ![Képernyőkép az irányítópult-csempe címének és alcímének megadásáról.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-tile-details.png)

    > [!IMPORTANT]
    > Amikor futtatja a mintaalkalmazást, és adatokat streamel az eseményközpontba, a csempe száma gyorsan változik (másodpercenként). Ennek az az oka, hogy Stream Analytics lekérdezés valójában másodpercenként frissíti **az értéket.** Frissítse a lekérdezést egy 3 perces, kétperces ablakra, hogy az utolsó néhány percben látható összeget lássa. 
11. Adjon hozzá egy másik vizualizációt. Ismételje meg az első néhány lépést:

    * Kattintson a **Csempe hozzáadása** elemre.
    * Válassza az **Egyedi streamelési adatok** lehetőséget. 
    * Kattintson a **Tovább** gombra.
    * Válassza ki az adatkészletét, majd kattintson a **Tovább** gombra. 

12. A **Vizualizáció típusa** területen válassza a **Vonaldiagram** lehetőséget.

13. A **Tengely alatt kattintson az** Érték hozzáadása **elemre,** majd válassza a `windowend` lehetőséget. 

14. Az **Értékek alatt** kattintson az Érték hozzáadása **elemre,** és válassza a `fraudulentuses` lehetőséget.

15. A **Megjelenítendő időtartomány** területen válassza ki az utolsó öt percet. Kattintson a **Tovább** gombra.

16. A csempe címeként adja meg a **Csalárd felhasználás megjelenítése az idő múlásával** címet, hagyja üresen az alcím mezőt, majd kattintson az **Alkalmaz** gombra. A rendszer automatikusan visszairányítja az irányítópultra.

17. Futtassa ismét az anomáliaérzékelő alkalmazást az adatok eseményközpontba való küldéséhez. Megjelenik a **Csalárd felhasználás** csempe, amely folyamatosan változik az adatok elemzésekor, és a vonaldiagram, amely megjeleníti az adatokat. 

    ![Képernyőkép a Power BI-eredményekről](./media/event-hubs-tutorial-visualize-anomalies/power-bi-results.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az összes létrehozott erőforrást el kívánja távolítani, távolítsa el a Power BI-vizualizáció adatait, majd törölje az erőforráscsoportot. Az erőforráscsoport törlésével a csoportban található összes erőforrás törlődik. Ebben az esetben eltávolítja az eseményközpontot, az Event Hubs-névteret, a Stream Analytics-feladatot, és magát az erőforráscsoportot is. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Erőforrások eltávolítása a Power BI-vizualizációban

Jelentkezzen be a Power BI-fiókjába. Lépjen a **Saját munkaterületre**. Az irányítópult nevét tartalmazó soron kattintson a kuka ikonra. Ezután lépjen az **Adatkészletek** területre, és törölje az adatkészletet (**contosoehdataset**) a kuka ikonra kattintva.

### <a name="clean-up-resources-using-azure-cli"></a>Az erőforrások eltávolítása az Azure CLI használatával

Az erőforráscsoport az [az group delete](/cli/azure/group#az_group_delete) paranccsal távolítható el.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="clean-up-resources-using-powershell"></a>Az erőforrások eltávolítása a PowerShell használatával

Az erőforráscsoport eltávolításához használja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * Eseményközpont létrehozása
> * Az eseményeket szimuláló és az eseményközpontba küldő alkalmazás futtatása
> * Stream Analytics-feladat konfigurálása a központnak küldött események feldolgozására
> * Power BI-vizualizáció konfigurálása az eredmények megjelenítésére

Folytassa a következő cikkel, ha többet szeretne megtudni az Azure Event Hubsról.

> [!div class="nextstepaction"]
> [Üzenetek küldése az Azure Event Hubsba a .NET Standardban – első lépések](event-hubs-dotnet-standard-getstarted-send.md)

[ingyenes fiók létrehozása]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
