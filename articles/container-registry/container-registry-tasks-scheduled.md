---
title: Oktatóanyag – ACR-feladat ütemezése
description: Ebből az oktatóanyagból megtudhatja, hogyan futtathat egy Azure Container Registry-feladatot egy meghatározott ütemezés szerint egy vagy több időzítő eseményindító beállításával
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: fa80bcbd318266a86c5bec08c9ee60fc0d22a10d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780856"
---
# <a name="tutorial-run-an-acr-task-on-a-defined-schedule"></a>Oktatóanyag: ACR-feladat futtatása meghatározott ütemezés szerint

Ez az oktatóanyag bemutatja, hogyan futtathat [ACR-feladatokat](container-registry-tasks-overview.md) ütemezés szerint. Feladat ütemezése egy vagy több időzítő *eseményindító beállításával.* Az időzítő eseményindítók használhatók önmagában vagy más feladat-eseményindítók együttesen is.

Ez az oktatóanyag a tevékenységek ütemezését és a következő műveleteket tartalmazza:

> [!div class="checklist"]
> * Feladat létrehozása időzítő eseményindítóval
> * Időzítő eseményindítók kezelése

A tevékenységek ütemezése az alábbihoz hasonló helyzetekben hasznos:

* Tároló számítási feladatainak futtatása ütemezett karbantartási műveletekhez. Például egy tárolóba ezett alkalmazás futtatásával eltávolíthatja a felesleges rendszerképeket a regisztrációs adatbázisból.
* Teszteket futtathat éles rendszerképen munkanap közben az élő webhely monitorozásának részeként.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="about-scheduling-a-task"></a>Tevékenység ütemezése

* **Eseményindító cron kifejezéssel** – A tevékenység időzítő eseményindítója egy *cron kifejezést használ.* A kifejezés egy olyan sztring, amely öt mezőben adja meg a perc, óra, nap, hónap és a hét napját a feladat aktiválása során. A percenkénti legfeljebb egy gyakoriság támogatott.

  A kifejezés például minden hétköznap délben `"0 12 * * Mon-Fri"` (UTC) aktivál egy feladatot. A [részleteket](#cron-expressions) a cikk későbbi, részletesen is olvashatja.
* **Többszörös időzítő eseményindítók** – Több időzítő hozzáadása egy tevékenységhez engedélyezett, ha az ütemezések eltérnek.
    * Több időzítő eseményindítót is megadhat a feladat létrehozásakor, vagy később hozzáadhatja őket.
    * A könnyebb felügyelet érdekében az eseményindítóknak nevet is ACR-feladatok, vagy meg kell adniuk az alapértelmezett eseményindítóneveket.
    * Ha az időzítő ütemezése egyszerre átfedésben van, a ACR-feladatok az ütemezett időpontban aktiválja a feladatot az egyes időzítők számára.
* **Egyéb feladat-eseményindítók** – Az időzítő által aktivált feladatokban [](container-registry-tutorial-build-task.md) forráskód véglegesítésen vagy alapként szolgáló rendszerkép-frissítéseken alapuló eseményindítókat [is engedélyezhet.](container-registry-tutorial-base-image-update.md) A többi ACR-feladathoz hasonlóan manuálisan [is][az-acr-task-run] futtathat ütemezett feladatokat.

## <a name="create-a-task-with-a-timer-trigger"></a>Feladat létrehozása időzítő eseményindítóval

### <a name="task-command"></a>Feladat parancs

Először töltse fel az alábbi héjkörnyezeti változót a környezetének megfelelő értékkel. Ez a lépés nem feltétlenül szükséges, de némileg könnyebbé teszi az oktatóanyagban lévő többsoros Azure CLI-parancsok végrehajtását. Ha nem tölti fel a környezeti változót, minden értéket manuálisan kell lecserélnie, amikor az megjelenik a példaparancsok között.

[![Indítás beágyazása](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell elindítása")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
```

Amikor az [az acr task create][az-acr-task-create] paranccsal hoz létre feladatot, igény szerint időzítő eseményindítót is hozzáadhat. Adja hozzá `--schedule` a paramétert, és adjon át egy cron kifejezést az időzítőnek.

Egyszerű példaként a következő feladat minden nap `hello-world` 21:00-kor (UTC Microsoft Container Registry aktiválja a rendszerkép futtatását a Microsoft Container Registry időpontban. A feladat forráskódkörnyezet nélkül fut.

```azurecli
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd mcr.microsoft.com/hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Futtassa [az az acr task show parancsot][az-acr-task-show] annak végrehajtásához, hogy az időzítő eseményindító konfigurálva van-e. Alapértelmezés szerint az alapként használható rendszerkép-frissítési eseményindító is engedélyezve van.

```azurecli
az acr task show --name timertask --registry $ACR_NAME --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
timertask linux       Enabled                           BASE_IMAGE, TIMER
```

## <a name="trigger-the-task"></a>A feladat aktiválása

Aktiválja manuálisan a feladatot [az az acr task run][az-acr-task-run] futtatásával, hogy megfelelően legyen beállítva:

```azurecli
az acr task run --name timertask --registry $ACR_NAME
```

Ha a tároló sikeresen fut, a kimenet az alábbihoz hasonló lesz. A kimenet a fő lépések megjelenítése érdekében tömörítve van

```output
Queued a run with ID: cf2a
Waiting for an agent...
2020/11/20 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2020/11/20 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2020/11/20 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Az ütemezett időpont után futtassa [az az acr task list-runs][az-acr-task-list-runs] parancsot annak ellenőrzéséhez, hogy az időzítő a várt módon aktiválta-e a feladatot:

```azurecli
az acr task list-runs --name timertask --registry $ACR_NAME --output table
```

Ha az időzítő sikeres, a kimenet az alábbihoz hasonló:

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
ca15      timertask  linux       Succeeded  Timer      2020-11-20T21:00:23Z  00:00:06
ca14      timertask  linux       Succeeded  Manual     2020-11-20T20:53:35Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Időzítő eseményindítók kezelése

Az [az acr task timer parancsokkal][az-acr-task-timer] kezelheti az ACR-feladatok időzítő eseményindítóit.

### <a name="add-or-update-a-timer-trigger"></a>Időzítő eseményindító hozzáadása vagy frissítése

A feladat létrehozása után igény szerint felvehet egy időzítő eseményindítót az [az acr task timer add paranccsal.][az-acr-task-timer-add] Az alábbi példa egy *timer2* nevű időzítő eseményindítót ad hozzá *a korábban létrehozott timertask* feladathoz. Ez az időzítő minden nap 10:30-kor (UTC) aktiválja a feladatot.

```azurecli
az acr task timer add \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Frissítse egy meglévő eseményindító ütemezését, vagy módosítsa az állapotát az [az acr task timer update paranccsal.][az-acr-task-timer-update] Például frissítse a *timer2* nevű eseményindítót úgy, hogy az 11:30 UTC időpontban aktiválja a feladatot:

```azurecli
az acr task timer update \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Időzítő eseményindítók listása

Az [az acr task timer list parancs][az-acr-task-timer-list] megjeleníti az egy feladathoz beállított időzítő eseményindítókat:

```azurecli
az acr task timer list --name timertask --registry $ACR_NAME
```

Példa a kimenetre:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Időzítő eseményindító eltávolítása

Az [az acr task timer remove paranccsal][az-acr-task-timer-remove] eltávolíthat egy időzítő eseményindítót egy feladatból. Az alábbi példa eltávolítja a *timer2* eseményindítót a *timertask feladatból:*

```azurecli
az acr task timer remove \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron-kifejezések

ACR-feladatok [az NCronTab](https://github.com/atifaziz/NCrontab) kódtárat használja a cron-kifejezések értelmezéséhez. A támogatott kifejezések ACR-feladatok kötelező mezőket tartalmaznak, szóközök elválasztva:

`{minute} {hour} {day} {month} {day-of-week}`

A cron-kifejezésekkel használt időzóna egyezményes világidő (UTC). Az órák formátuma 24 órás.

> [!NOTE]
> ACR-feladatok cron-kifejezésekben nem támogatja a vagy a `{second}` `{year}` mezőt. Ha egy másik rendszerben használt cron kifejezést másol, ne távolítsa el ezeket a mezőket, ha használják őket.

Minden mező az alábbi értéktípusok egyikét tartalmazhatja:

|Típus  |Példa  |Aktiváláskor  |
|---------|---------|---------|
|Egy adott érték |<nobr>`"5 * * * *"`</nobr>|óránként, az elmúlt 5 percben|
|Minden érték ( `*` )|<nobr>`"* 5 * * *"`</nobr>|az 5:00-tól kezdődő óra minden percében (naponta 60 alkalommal)|
|Egy tartomány ( `-` operátor)|<nobr>`"0 1-3 * * *"`</nobr>|Naponta 3 alkalommal, 1:00-kor, 2:00-kor és 3:00-kor (UTC)|
|Értékek halmaza ( `,` operátor)|<nobr>`"20,30,40 * * * *"`</nobr>|Óránként 3 alkalommal, 20 perc, 30 perc és 40 perc az elmúlt órában|
|Intervallumérték ( `/` operátor)|<nobr>`"*/10 * * * *"`</nobr>|Óránként 6 alkalommal, 10 perc, 20 perc és így tovább, az elmúlt egy órában

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron-példák

|Példa|Aktiváláskor  |
|---------|---------|
|`"*/5 * * * *"`|öt percenként egyszer|
|`"0 * * * *"`|óránként egyszer|
|`"0 */2 * * *"`|kétórás|
|`"0 9-17 * * *"`|óránként 9:00 és 17:00 között (UTC)|
|`"30 9 * * *"`|minden nap 9:30-kor (UTC)|
|`"30 9 * * 1-5"`|minden hétköznap 9:30-kor (UTC)|
|`"30 9 * Jan Mon"`|januárban minden hétfőn 9:30-kor (UTC)|

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani az oktatóanyag-sorozatban létrehozott összes erőforrást, beleértve a tárolóregisztráló adatbázist vagy beállításjegyzékeket, a tárolópéldányt, a kulcstartót és a szolgáltatásnévt, a következő parancsokat kell kivenni:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre Azure Container Registry időzítő által automatikusan aktivált feladatokat. 

Egy példa arra, hogyan lehet ütemezett feladattal megtisztítani a regisztrációs adatbázisban található adattárakat: [Automatically purge images from an Azure container registry](container-registry-auto-purge.md)(Lemezképek automatikus kiürítés egy Azure-beli tároló-beállításjegyzékből).

A forráskód véglegesítései vagy az alapként való rendszerkép-frissítések által aktivált feladatok példáiért tekintse meg az [oktatóanyag-sorozat ACR-feladatok cikkeit.](container-registry-tutorial-quick-task.md)



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az_acr_task_timer_add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az_acr_task_timer_remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az_acr_task_timer_list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az_acr_task_timer_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
