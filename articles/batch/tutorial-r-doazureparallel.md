---
title: Oktatóanyag – párhuzamos R-szimuláció futtatása Azure Batch
description: Oktatóanyag – részletes útmutató Monte Carlo pénzügyi szimulációjának futtatásához az Azure Batchben az R doAzureParallel csomag használatával
ms.devlang: r
ms.topic: tutorial
ms.date: 10/08/2020
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 7e74a3b02be35b40e1a92bef269a3aed0b2313e4
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491775"
---
# <a name="tutorial-run-a-parallel-r-simulation-with-azure-batch"></a>Oktatóanyag: párhuzamos R-szimuláció futtatása az Azure Batch segítségével

Futtassa párhuzamos R számítási feladatait skálázva a kis méretű [doAzureParallel](https://www.github.com/Azure/doAzureParallel) R-csomag segítségével, amely lehetővé teszi az Azure Batch használatát közvetlenül az R-munkamenetből. A doAzureParallel csomag a népszerű [foreach](https://cran.r-project.org/web/packages/foreach/index.html) R-csomagra épül. A doAzureParallel a foreach ciklus minden ismétlését Azure Batch-műveletként küldi el.

Ez az oktatóanyag bemutatja, hogyan helyezhet üzembe egy Batch-készletet és futtathat párhuzamos R-feladatot az Azure Batchben közvetlenül az RStudión belül. Az alábbiak végrehajtásának módját ismerheti meg:


> [!div class="checklist"]
> * A doAzureParallel telepítése és konfigurálása a Batch- és tárfiókok eléréséhez
> * Batch-készlet létrehozása párhuzamos háttérként az R-munkamenethez
> * Párhuzamos mintaszimuláció futtatása a készleten

## <a name="prerequisites"></a>Előfeltételek

* Egy telepített [R](https://www.r-project.org/)-disztribúció, például a [Microsoft R Open](https://mran.microsoft.com/open). Az R 3.3.1-es vagy újabb verziója.

* Az [RStudio](https://www.rstudio.com/) kereskedelmi kiadása, vagy a nyílt forráskódú [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop).

* Azure Batch-fiók és Azure Storage-fiók. A fiókok létrehozásához tekintse meg a Batch az [Azure Portallal](quick-create-portal.md) vagy az [Azure CLI-vel](quick-create-cli.md) történő használatát ismertető rövid útmutatókat.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]
## <a name="install-doazureparallel"></a>A doAzureParallel telepítése

A RStudio-konzolon telepítse a [DoAzureParallel GitHub-csomagot](https://www.github.com/Azure/doAzureParallel). Az alábbi parancsok letöltik és telepítik a csomagot és függőségeit az aktuális R-munkamenetbe:

```R
# Install the devtools package
install.packages("devtools")

# Install rAzureBatch package
devtools::install_github("Azure/rAzureBatch")

# Install the doAzureParallel package
devtools::install_github("Azure/doAzureParallel")

# Load the doAzureParallel library
library(doAzureParallel)
```
A telepítés több percig is eltarthat.

A doAzureParallel korábban beszerzett fiókhitelesítő adatokkal történő konfigurálásához hozzon létre egy *credentials.json* nevű konfigurációs fájlt a munkakönyvtárban:

```R
generateCredentialsConfig("credentials.json")
```

Töltse fel a fájlt Batch- és tárfiókjának neveivel és kulcsaival. Ne változtassa meg a `githubAuthenticationToken` beállítást.

Az elkészült hitelesítőadat-fájl a következőhöz hasonló:

```json
{
  "batchAccount": {
    "name": "mybatchaccount",
    "key": "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==",
    "url": "https://mybatchaccount.mybatchregion.batch.azure.com"
  },
  "storageAccount": {
    "name": "mystorageaccount",
    "key": "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=="
  },
  "githubAuthenticationToken": ""
}
```

Mentse a fájlt. Ezután futtassa a következő parancsot az aktuális R-munkamenet hitelesítő adatainak beállításához:

```R
setCredentials("credentials.json")
```

## <a name="create-a-batch-pool"></a>Batch-készlet létrehozása

A doAzureParallel tartalmaz egy függvényt, amely egy Azure Batch-készletet (fürtöt) hoz létre párhuzamos R-feladatok futtatására. A csomópontok egy Ubuntu-alapú [Azure adatelemzési virtuális gépet](../machine-learning/data-science-virtual-machine/overview.md) futtatnak. A Microsoft R Open és más népszerű R-csomagok előre telepítve vannak ezen a képen. Megtekinthet és testre szabhat bizonyos fürtbeállításokat, például a csomópontok számát és méretét.

Fürtkonfigurációs JSON-fájl létrehozása a munkakönyvtárban:

```R
generateClusterConfig("cluster.json")
```

Nyissa meg a fájlt az alapértelmezett konfiguráció megtekintéséhez, amely 3 dedikált csomópontot és 3 [alacsony prioritású](batch-low-pri-vms.md) csomópontot tartalmaz. Ezek a beállítások csupán példák, kísérletezhet velük, módosíthatja őket. A dedikált csomópontok a készlet számára vannak fenntartva. Az alacsony prioritású csomópontok kedvezményes áron érhetők el az Azure többlet VM-kapacitásából. Ha az Azure nem rendelkezik elegendő kapacitással, az alacsony prioritású csomópontok elérhetetlenné válnak.

Az oktatóanyag használatakor módosítsa a konfigurációt az alábbiak szerint:

* A csomópontok mindkét magjának kihasználásához növelje a `taskSlotsPerNode` elemet *2* értékre
* Állítsa be a `dedicatedNodes` elemet *0* értékre, hogy kipróbálhassa a Batch számára elérhető alacsony prioritású virtuális gépeket. Állítsa be a `lowPriorityNodes` elem `min` elemét *5* értékre, a `max` elemet pedig *10* értékre, vagy válasszon kisebb számokat, ha szükséges.

Hagyja meg az alapértelmezett értékeket a többi beállításnál, és mentse a fájlt. Ennek a következőképpen kell kinéznie:

```json
{
  "name": "myPoolName",
  "vmSize": "Standard_D2_v2",
  "taskSlotsPerNode": 2,
  "poolSize": {
    "dedicatedNodes": {
      "min": 0,
      "max": 0
    },
    "lowPriorityNodes": {
      "min": 5,
      "max": 10
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": []
}
```

Hozza létre a fürtöt. A Batch azonnal létrehozza a készletet, de a számítási csomópontok lefoglalása és elindítása igénybe vesz néhány percet. Miután a fürt elérhetővé vált, regisztrálja az R-munkamenet párhuzamos háttereként.

```R
# Create your cluster if it does not exist; this takes a few minutes
cluster <- makeCluster("cluster.json")

# Register your parallel backend
registerDoAzureParallel(cluster)

# Check that the nodes are running
getDoParWorkers()
```

A kimenet a doAzureParallel „futtatási feldolgozóinak” számát mutatja. Ez a szám a csomópontok száma a `taskSlotsPerNode` értékkel megszorozva. Ha a fürtkonfigurációt a korábban leírtak szerint módosította, a szám *10*.

## <a name="run-a-parallel-simulation"></a>Egy párhuzamos szimuláció futtatása

Most, hogy létrehozta a fürtöt, készen áll a foreach ciklus futtatására a regisztrált párhuzamos háttérrel (Azure Batch-készlet). A szemléltetéshez futtasson egy Monte Carlo pénzügyi szimulációt, először egy standard foreach ciklus használatával helyileg, majd a Batch segítségével futtatva a foreach ciklust. Ez a példa egy árfolyam nagy mennyiségű különböző, 5 év utáni eredmények szimulálásával megalkotott előrejelzésének egy egyszerűsített változata.

Tegyük fel, hogy a Contoso Corporation árfolyama nyitó árának 1,001-szeresével növekszik átlagosan naponta, de ingadozása (standard szórása) 0,01. 100 dollár induló ár mellett használjon egy Monte Carlo árképzési szimulációt a Contoso árfolyamának kiszámítására 5 év eltelte után.

A Monte Carlo szimuláció paraméterei:

```R
mean_change = 1.001
volatility = 0.01
opening_price = 100
```

Záróárak szimulálásához adja meg a következő függvényt:

```R
getClosingPrice <- function() {
  days <- 1825 # ~ 5 years
  movement <- rnorm(days, mean=mean_change, sd=volatility)
  path <- cumprod(c(opening_price, movement))
  closingPrice <- path[days]
  return(closingPrice)
}
```

Először futtasson 10 000 szimulációt helyileg egy standard foreach ciklus használatával a `%do%` kulcsszóval:

```R
start_s <- Sys.time()
# Run 10,000 simulations in series
closingPrices_s <- foreach(i = 1:10, .combine='c') %do% {
  replicate(1000, getClosingPrice())
}
end_s <- Sys.time()
```


Rajzolja meg a záróárakat egy hisztogramban az eredmények eloszlásának megjelenítéséhez:

```R
hist(closingPrices_s)
```

A kimenet a következőkhöz hasonló:

![Képernyőfelvétel – a záró árak hisztogramja](media/tutorial-r-doazureparallel/closing-prices-local.png)
  
Egy helyi szimuláció néhány másodperc vagy kevesebb alatt befejeződik:

```R
difftime(end_s, start_s)
```

A becsült futásidő 10 millió eredményre helyileg, lineáris közelítés használatával körülbelül 30 perc:

```R
1000 * difftime(end_s, start_s, unit = "min")
```


Most futtassa a kódot `foreach` használatával a `%dopar%` kulcsszóval, hogy összehasonlítsa, mennyi ideig tart 10 millió szimuláció futtatása az Azure-ban. A szimuláció Batchcsel történő összevetéséhez futtassa 100 000 szimuláció 100 ismétlését:

```R
# Optimize runtime. Chunking allows running multiple iterations on a single R instance.
opt <- list(chunkSize = 10)
start_p <- Sys.time()
closingPrices_p <- foreach(i = 1:100, .combine='c', .options.azure = opt) %dopar% {
  replicate(100000, getClosingPrice())
}
end_p <- Sys.time()
```

A szimuláció kiosztja a tevékenységeket a Batch-készlet csomópontjainak. A tevékenység a Azure Portalban lévő készlethez tartozó Heat térképen látható. Nyissa meg a **Batch-fiókok**  >  *myBatchAccount*. Kattintson a **készletek**  >  *myPoolName* elemre.

![Párhuzamos R-feladatokat futtató készlet hőtérképe](media/tutorial-r-doazureparallel/pool.png)

Néhány perc múlva a szimuláció befejeződik. A csomag automatikusan egyesíti az eredményeket, és lekéri őket a csomópontokból. Ezt követően készen áll az eredmények R-munkamenetben való használatára.

```R
hist(closingPrices_p)
```

A kimenet a következőkhöz hasonló:

![Záróárak eloszlása](media/tutorial-r-doazureparallel/closing-prices.png)

Mennyi ideig tartott a párhuzamos szimuláció?

```R
difftime(end_p, start_p, unit = "min")
```

Látható, hogy a szimuláció Batch-készletben történő futtatása jelentősen növeli a teljesítményt a helyi futtatás várt idejével szemben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután a feladat befejeződött, automatikusan törlődik. Ha a fürtre már nincs szükség, hívja meg a `stopCluster` függvényt a doAzureParallel csomagban a törléséhez:

```R
stopCluster(cluster)
```

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> A doAzureParallel telepítése és konfigurálása a Batch- és tárfiókok eléréséhez
> * Batch-készlet létrehozása párhuzamos háttérként az R-munkamenethez
> * Párhuzamos mintaszimuláció futtatása a készleten


A doAzureParallellel kapcsolatos további információkért tekintse meg a GitHubon található dokumentációt és mintákat.

> [!div class="nextstepaction"]
> [doAzureParallel csomag](https://github.com/Azure/doAzureParallel/)
