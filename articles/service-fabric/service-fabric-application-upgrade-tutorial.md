---
title: Service Fabric alkalmazás verziófrissítésének oktatóanyaga
description: Ez a cikk végigvezeti egy Service Fabric alkalmazás üzembe helyezésének, a kód módosításának és a Visual Studio használatával történő frissítésének folyamatán.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: acde2f4e51bee29d2eefb0d5fbb54fbe421a41f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996236"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Service Fabric alkalmazás-frissítési oktatóanyag a Visual Studióval
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Az Azure Service Fabric leegyszerűsíti a felhőalapú alkalmazások verziófrissítésének folyamatát azáltal, hogy csak a megváltozott szolgáltatásokat frissíti, és az alkalmazás állapotát a frissítési folyamat során figyeli. Emellett automatikusan Visszagörgeti az alkalmazást az előző verzióra, amikor problémákba ütközik. Service Fabric az alkalmazások frissítése *nulla állásidő*, mivel az alkalmazás leállás nélkül frissíthető. Ez az oktatóanyag bemutatja, hogyan végezheti el a működés közbeni frissítését a Visual studióból.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>1. lépés: a vizuális objektumok mintájának összeállítása és közzététele
Először töltse le a [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) alkalmazást a githubról. Ezután hozza létre és tegye közzé az alkalmazást úgy, hogy a jobb gombbal az alkalmazás projektre, a **VisualObjects**, majd a **publish (közzététel** ) parancsra kattint a Service Fabric menüpontban.

![Service Fabric alkalmazás helyi menüje][image1]

A **publish (közzététel** ) elem kiválasztásával felugró ablak jelenik meg, és beállíthatja a **célként megadott profilt** **PublishProfiles\Local.xml**. Az ablaknak a következőhöz hasonlóan kell kinéznie, mielőtt rákattint a **publish (közzététel**) gombra.

![Service Fabric alkalmazás közzététele][image2]

Most kattintson a **Közzététel** lehetőségre a párbeszédpanelen. [A Service Fabric Explorer használatával megtekintheti a fürtöt és az alkalmazást](service-fabric-visualizing-your-cluster.md). A vizualizáció objektumok alkalmazás egy webszolgáltatással rendelkezik, amelyet a böngésző címsorának beírásával léphet be `http://localhost:8081/visualobjects/` .  A képernyőn körülbelül 10 lebegő vizualizációs objektumot kell látni.

**Megjegyzés:** Ha `Cloud.xml` a profilt (Azure Service Fabric) telepíti, akkor az alkalmazásnak a **http://{ServiceFabricName} címen kell elérhetőnek lennie. { Régió}. cloudapp. Azure. com: 8081/visualobjects/**. Győződjön meg arról, hogy `8081/TCP` konfigurálva van a Load Balancerban (keresse meg a Load Balancer ugyanabban az erőforráscsoporthoz, mint a Service Fabric példányt).

## <a name="step-2-update-the-visual-objects-sample"></a>2. lépés: a vizuális objektumok mintájának frissítése
Észreveheti, hogy az 1. lépésben üzembe helyezett verziónál a vizualizációs objektumok nem forognak. Frissítjük az alkalmazást arra a szintre, ahol a vizualizáció objektumok is forognak.

Válassza ki a VisualObjects. ActorService projektet a VisualObjects-megoldáson belül, majd nyissa meg a **VisualObjectActor. cs** fájlt. A fájlon belül lépjen a metódusra `MoveObject` , a megjegyzésbe `visualObject.Move(false)` és a megjegyzésbe `visualObject.Move(true)` . A kód módosítása a szolgáltatás frissítése után elforgatja az objektumokat.  **Most már létrehozhatja (nem építheti újra) a megoldást**, amely létrehozza a módosított projekteket. Ha az *összes Újraépítés* lehetőséget választja, frissítenie kell az összes projekt verzióját.

Az alkalmazás verziószámát is meg kell adni. Ha módosítani szeretné a verziót, miután a jobb gombbal a **VisualObjects** projektre kattint, használhatja a Visual Studio **jegyzékfájl-verziójának szerkesztése** lehetőséget. A beállítás kiválasztásával megjelenik a kiadási verziók párbeszédpanelje a következő módon:

![Verziószámozás párbeszédpanel][image3]

Frissítse a módosított projektek és a hozzájuk tartozó csomagok verzióját, valamint az alkalmazást a 2.0.0 verzióra. A módosítások elvégzése után a jegyzékfájlnak a következőhöz hasonlóan kell kinéznie (a félkövér részek a változásokat mutatják):

![Verziók frissítése][image4]

A Visual Studio-eszközök az **alkalmazások és szolgáltatások verzióinak automatikus frissítését követően automatikusan** kumulatív verziókat is elvégezhetnek. Ha a [SemVer](http://www.semver.org)-t használja, akkor a kódot és/vagy a konfigurációs csomag verzióját csak akkor kell frissíteni, ha ez a beállítás be van jelölve.

Mentse a módosításokat, és most jelölje be az **alkalmazás frissítése** jelölőnégyzetet.

## <a name="step-3--upgrade-your-application"></a>3. lépés: az alkalmazás frissítése
Ismerkedjen meg az [alkalmazás frissítési paramétereivel](service-fabric-application-upgrade-parameters.md) és a [frissítési folyamattal](service-fabric-application-upgrade.md) , és Ismerje meg a különböző frissítési paramétereket, időtúllépéseket és az alkalmazandó állapotra vonatkozó feltételeket. Ebben az útmutatóban a szolgáltatás állapotának kiértékelése feltétel az alapértelmezett (nem figyelt üzemmód) értékre van állítva. Ezeket a beállításokat úgy is megadhatja, ha kiválasztja a **frissítési beállítások konfigurálása** lehetőséget, majd igény szerint módosítja a paramétereket.

Most már készen áll az alkalmazás frissítésének elindítására a **publish (közzététel**) lehetőség kiválasztásával. Ez a beállítás frissíti az alkalmazást a verzió 2.0.0, amelyben az objektumok elforgatva vannak. Service Fabric egyszerre csak egy frissítési tartományt frissít (egyes objektumokat először frissítik, majd mások követik), és a szolgáltatás a frissítés során továbbra is elérhető marad. A szolgáltatáshoz való hozzáférést az ügyfélen (böngészőn) keresztül lehet ellenőrizni.  

Most, ahogy az alkalmazás frissítése bekövetkezik, nyomon követheti Service Fabric Explorer használatával, az alkalmazások alatt lévő **frissítések folyamatban** lapon.

Néhány percen belül minden frissítési tartományt frissíteni kell (befejezett), és a Visual Studio kimeneti ablakának azt is tartalmaznia kell, hogy a frissítés befejeződött. Ekkor a böngészőablakban található *összes* vizualizációs objektum már forog!

Érdemes lehet megváltoztatnia a verziókat, és az 2.0.0 verzióról a 3.0.0 verzióra, vagy akár a 2.0.0 verzióról a 1.0.0 verzióra vált. Az időkorláttal és az állapottal foglalkozó szabályzatokkal megismerheti őket. Ha egy Azure-fürtre helyez üzembe egy helyi fürt helyett, a használt paraméterek eltérőek lehetnek. Javasoljuk, hogy a konzervatív időkorlátot állítsa be.

## <a name="next-steps"></a>Következő lépések
[Az alkalmazás PowerShell használatával történő frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti az alkalmazás frissítésén a PowerShell használatával.

A frissítési paraméterek használatával szabályozhatja az alkalmazás [frissítésének](service-fabric-application-upgrade-parameters.md)módját.

Az alkalmazások frissítését az [adatszerializálás](service-fabric-application-upgrade-data-serialization.md)használatának megismerésével teheti meg.

Ismerje meg, hogyan használhatja a speciális funkciókat az alkalmazás frissítéséhez a [speciális témakörökre](service-fabric-application-upgrade-advanced.md)való hivatkozással.

Az alkalmazások frissítéseinek [hibaelhárításával](service-fabric-application-upgrade-troubleshooting.md)kapcsolatos gyakori problémák elhárítása.

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
