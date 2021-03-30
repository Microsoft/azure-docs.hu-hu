---
title: A Azure DevTest Labs és a DevOps integrációja | Microsoft Docs
description: Ismerje meg, hogyan használhatók a Azure DevTest Labs Labs a folyamatos integrációs (CI)/folyamatos továbbítási (CD) folyamatokon belül egy vállalati környezetben.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8a5d35a541e079b7d39cae2ec43da608274533f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85481068"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>A Azure DevTest Labs és az Azure DevOps integrációja
A DevOps egy szoftverfejlesztői módszer, amely integrálja a szoftverfejlesztői (dev) rendszereket a rendszerekhez tartozó műveletekkel (Ops). Ez a rendszer az üzleti célokkal való összhangban új funkciókat, frissítéseket és javításokat biztosíthat. Ez a módszer magában foglalja az új funkciók megtervezését a célok, a használati minták és a felhasználói visszajelzések alapján. a rendszerek javítása, helyreállítása és megerősítése a problémák bekövetkezésekor. Ennek a módszernek a könnyen azonosítható összetevője a folyamatos integráció (CI)/folyamatos továbbítás (CD) folyamata. A CI/CD-folyamatok az adatok, a kódok és az erőforrások elvégzését végzik el egy sorozattal, amely magában foglalja a létrehozási, tesztelési és üzembe helyezési műveleteket a rendszer előállításához. Ez a cikk azt ismerteti, hogyan lehet hatékonyan használni a laborokat egy folyamaton belül egy vállalati környezetben. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>A Labs használatának előnyei a DevOps-munkafolyamatban 

### <a name="focused-access"></a>Célzott hozzáférés 
A laborok összetevőként való használata lehetővé teszi, hogy egy adott ökoszisztéma egy korlátozott számú csoporttal legyen társítva. Általában egy közös területen vagy egy adott szolgáltatásban dolgozó csapatnak vagy csoportnak hozzá kell rendelnie egy labort.   

### <a name="infrastructure-replication-in-the-cloud"></a>Infrastruktúra-replikáció a felhőben 
A fejlesztők gyorsan létrehozhatnak egy fejlesztési ökoszisztémát, amely tartalmaz egy forráskódot és eszközöket tartalmazó fejlesztői listát. A fejlesztő létrehozhat olyan környezetet is, amely közel azonos az éles konfigurációval. Segít a gyorsabb belső hurok-fejlesztésben. 

### <a name="pre-production"></a>Éles üzem előtti 
Ha a CI/CD-folyamaton belül labort használ, egyszerűbbé válik több különböző üzem előtti környezet vagy gép használata az aszinkron teszteléshez. A különböző támogatási infrastruktúrák, például az építő ügynökök üzembe helyezhetők és felügyelhetők a laborban. 

## <a name="devops-with-devtest-labs"></a>DevOps az DevTest Labs szolgáltatással 

### <a name="development--operation"></a>Fejlesztés/művelet 
A labort olyan csapatra kell összpontosítani, amely egy szolgáltatás területén működik. Ez a közös fókusz lehetővé teszi a területtel kapcsolatos erőforrások, például az eszközök, a parancsfájlok vagy a Resource Manager-sablonok megosztását. Gyorsabb változásokat tesz lehetővé, miközben a negatív hatásokat egy kisebb csoportra korlátozza. Ezek a megosztott erőforrások lehetővé teszik, hogy a fejlesztő fejlesztési virtuális gépeket hozzon létre az összes szükséges kóddal, eszközzel és konfigurációval. Létrehozhatók akár dinamikusan, akár olyan rendszerrel, amely alapszintű lemezképeket hoz létre a testreszabásokkal. Nem csak a fejlesztők hozhatnak létre virtuális gépeket, de létrehozhatnak DevTest Labs-környezeteket is a szükséges sablonok alapján a megfelelő Azure-erőforrások létrehozásához a laborban. Bármilyen módosítást vagy roncsolásos munkát végezhet a tesztkörnyezet környezetében anélkül, hogy ez hatással lenne másnak. Vegye figyelembe azt a forgatókönyvet, ahol a termék az ügyfél gépén telepített önálló rendszer. Ebben az esetben a DevTest Labs továbbfejlesztette a virtuális gépek létrehozását, amely magában foglalja a további szoftverek telepítését az összetevők használatával, valamint a felhasználói konfigurációk előkészítését a kód gyorsabb belső hurok-teszteléséhez. 
  
## <a name="cicd-pipeline"></a>CI-/CD-folyamat 
A CI/CD-folyamat a DevOps egyik kritikus összetevője, amely kódot helyez át a fejlesztőtől egy lekéréses kérelemből, integrálja azt a meglévő kóddal, és üzembe helyezi az üzemi ökoszisztémában. Az összes erőforrásnak nem kell laboron belül lennie. Egy Jenkins-gazdagép például a laboron kívül is beállítható egy állandó erőforrásként. Íme néhány példa arra, hogyan integrálhatja a laborokat a folyamatba. 

### <a name="build"></a>Létrehozás 
A létrehozási folyamat arra összpontosít, hogy létrehozzon egy olyan összetevő-csomagot, amelyet a rendszer a kiadási folyamatba való átadásra fog végezni. A laborok a build folyamat részeként a Build-ügynökök és más támogatási erőforrások helyeként is szerepelhetnek. Az infrastruktúra dinamikus kiépítésének lehetősége nagyobb szabályozást tesz lehetővé. Ha több környezettel rendelkezik a laborban, minden egyes Build aszinkron módon futtatható, miközben a környezeti információk részeként a Build ID-t használja, hogy egyedileg azonosítsa az erőforrásokat az adott buildhez.   

A Build-ügynökök esetében a labor a hozzáférés korlátozásával növeli a biztonságot, és csökkenti a véletlen sérülés lehetőségét.  

### <a name="test"></a>Tesztelés 
A DevTest Labs lehetővé teszi a CI/CD-folyamatok számára az automatikus és manuális teszteléshez használható Azure-erőforrások (virtuális gépek, környezetek) létrehozásának automatizálását. A virtuális gépek olyan összetevők vagy képletek használatával jönnek létre, amelyek a létrehozási folyamatból származó adatokat használják a teszteléshez szükséges különböző egyéni konfigurációk létrehozásához.   

### <a name="release"></a>Kiadás 
A DevTest Labs szolgáltatást általában a kód üzembe helyezése előtt a kiadás szakaszban lehet ellenőrizni. Ez hasonló a létrehozás szakasz teszteléséhez. Az üzemi erőforrások nem helyezhetők üzembe a DevTest Labs szolgáltatásban. 

### <a name="customization"></a>Testreszabás 
Az Azure DevOps léteznek olyan meglévő feladatok, amelyek lehetővé teszik a virtuális gépek és a környezetek kezelését egy adott laboron belül. Míg az Azure DevOps Services a CI/CD-folyamatok kezelésének egyik módja, a labort bármely olyan rendszerbe integrálhatja, amely támogatja a REST API-k meghívását, a PowerShell-parancsfájlok futtatását vagy az Azure CLI használatát. 

Míg egyes CI/CD-folyamatok kezelői olyan nyílt forráskódú beépülő modulokkal rendelkeznek, amelyek az Azure-ban és a DevTest Labs-ban is kezelhetik az erőforrásokat. Előfordulhat, hogy néhány egyéni parancsfájlt kell használnia, hogy illeszkedjen a folyamat konkrét igényeihez.  Ezt szem előtt tartva a feladatok végrehajtásakor a rendszer egy egyszerű szolgáltatásnevet használ a megfelelő szerepkörrel, hogy hozzáférjen a laborhoz. Az egyszerű szolgáltatásnak általában szüksége van a közreműködő szerepkörhöz a laborhoz való hozzáférésre. További információ: az [Azure DevTests Labs integrálása az Azure DevOps folyamatos integrációs és szállítási folyamatba](devtest-lab-integrate-ci-cd.md). 
 