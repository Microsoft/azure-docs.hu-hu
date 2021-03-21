---
title: 'ML Studio (klasszikus): áttelepítés Azure Machine Learning – R-szkript végrehajtása'
description: Az újraépített Studio (klasszikus) végrehajtja az R parancsfájl-modulokat, hogy Azure Machine Learningon fussanak.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: ac9ad296029451d624345d8b3bb365d881ba9a84
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565034"
---
# <a name="migrate-execute-r-script-modules-in-studio-classic"></a>Az R script-modulok migrálása a Studióban (klasszikus)

Ebből a cikkből megtudhatja, hogyan lehet újraépíteni a Studio (klasszikus) **R script** modult a Azure Machine learning-ben.

A studióból (klasszikus) való áttelepítéssel kapcsolatos további információkért tekintse meg az [áttelepítés áttekintése című cikket](migrate-overview.md).

## <a name="execute-r-script"></a>R-szkript végrehajtása

A Azure Machine Learning Designer mostantól Linux rendszeren fut. A Studio (klasszikus) a Windows rendszeren fut. A platform változása miatt az áttelepítés során módosítania kell a **végrehajtási R-szkriptet** , ellenkező esetben a folyamat sikertelen lesz.

Ha a Studio (klasszikus) alkalmazásból szeretne áttelepíteni egy **végrehajtási R-parancsfájlt** , a `maml.mapInputPort` és az `maml.mapOutputPort` interfészeket a standard functions elemre kell cserélnie.

Az alábbi táblázat az R-parancsfájl moduljának változásait foglalja össze:

|Szolgáltatás|Studio (klasszikus)|Azure Machine Learning Designer|
|---|---|---|
|Parancsfájl felülete|`maml.mapInputPort` és `maml.mapOutputPort`|Függvény felülete|
|Platform|Windows|Linux|
|Internetről elérhető |Nem|Igen|
|Memória|14 GB|Számítási SKU-tól függ|

### <a name="how-to-update-the-r-script-interface"></a>Az R-parancsfájl felületének frissítése

Íme egy példa a Studio (klasszikus) **R script** moduljának végrehajtására:
```r
# Map 1-based optional input ports to variables 
dataset1 <- maml.mapInputPort(1) # class: data.frame 
dataset2 <- maml.mapInputPort(2) # class: data.frame 

# Contents of optional Zip port are in ./src/ 
# source("src/yourfile.R"); 
# load("src/yourData.rdata"); 

# Sample operation 
data.set = rbind(dataset1, dataset2); 

 
# You'll see this output in the R Device port. 
# It'll have your stdout, stderr and PNG graphics device(s). 

plot(data.set); 

# Select data.frame to be sent to the output Dataset port 
maml.mapOutputPort("data.set"); 
```

A tervezőben a frissített tartalmak láthatók. Figyelje meg, hogy a `maml.mapInputPort` és a `maml.mapOutputPort` standard Function felülettel lett lecserélve `azureml_main` . 
```r
azureml_main <- function(dataframe1, dataframe2){ 
    # Use the parameters dataframe1 and dataframe2 directly 
    dataset1 <- dataframe1 
    dataset2 <- dataframe2 

    # Contents of optional Zip port are in ./src/ 
    # source("src/yourfile.R"); 
    # load("src/yourData.rdata"); 

    # Sample operation 
    data.set = rbind(dataset1, dataset2); 


    # You'll see this output in the R Device port. 
    # It'll have your stdout, stderr and PNG graphics device(s). 
    plot(data.set); 

  # Return datasets as a Named List 

  return(list(dataset1=data.set)) 
} 
```
További információ: az [R parancsfájl-végrehajtási modul referenciája](../algorithm-module-reference/execute-r-script.md).

### <a name="install-r-packages-from-the-internet"></a>R-csomagok telepítése az internetről

Azure Machine Learning Designer segítségével közvetlenül a CRANből telepítheti a csomagokat.

Ez a Studio (klasszikus) továbbfejlesztése. Mivel a Studio (klasszikus) egy internet-hozzáféréssel nem rendelkező sandbox-környezetben fut, további csomagok telepítéséhez egy zip-csomagban kell parancsfájlokat feltöltenie. 

A következő kód használatával telepítheti a CRAN-csomagokat a tervező **végrehajtási R-parancsfájl** moduljában:
```r
  if(!require(zoo)) { 
      install.packages("zoo",repos = "http://cran.us.r-project.org") 
  } 
  library(zoo) 
```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan telepítheti át az R parancsfájl-modulokat Azure Machine Learningba.

Tekintse meg a Studio (klasszikus) áttelepítési sorozat további cikkeit:

1. [Áttelepítési áttekintés](migrate-overview.md).
1. [Adatkészlet Átmigrálása](migrate-register-dataset.md).
1. [Építse újra a Studio (klasszikus) betanítási folyamatát](migrate-rebuild-experiment.md).
1. [Építse újra a Studio (klasszikus) webszolgáltatást](migrate-rebuild-web-service.md).
1. [Azure Machine learning webszolgáltatások integrálása az ügyfélalkalmazások alkalmazásával](migrate-rebuild-integrate-with-client-app.md).
1. **Telepítse át az R-szkriptek végrehajtási modulját**.