---
title: Tárolók feladatátvétele és méretezése
description: Ez az oktatóanyag bemutatja a feladatátvételek kezelését egy Azure Service Fabric-tárolóalkalmazásban.  Emellett megismerheti a fürtben futó tárolók és szolgáltatások méretezésének módját.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: f51a195f9ad43c7facb7dc413a4da83fc6b202b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91531394"
---
# <a name="tutorial-demonstrate-fail-over-and-scaling-of-container-services-with-service-fabric"></a>Oktatóanyag: Tárolószolgáltatások Service Fabrickel történő feladatátvételének és méretezésének bemutatása

Ez az oktatóanyag egy sorozat harmadik része. Ez az oktatóanyag bemutatja a feladatátvételek kezelését Service Fabric-tárolóalkalmazásokban. Emellett megismerheti a tárolók méretezésének módját is. Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * További tudnivalók a tárolók feladatátvételéről egy Service Fabric-fürtben
> * A webes előtértárolók méretezése egy alkalmazásban

## <a name="prerequisites"></a>Előfeltételek

A [2.részben](service-fabric-tutorial-package-containers.md) bemutatott alkalmazás egy aktív Service Fabric-fürtön fut.

## <a name="fail-over-a-container-in-a-cluster"></a>Tároló feladatátvétele a fürtben

A Service Fabric biztosítja, hogy meghibásodás esetén a tárolópéldányok automatikusan áthelyeződjenek a fürt más csomópontjaira. Manuálisan is ürítheti a tárolók csomópontjait, és azokat zökkenőmentesen helyezheti át a fürt más csomópontjaira. A szolgáltatásokat többféleképpen is méretezheti – ebben a példában a Service Fabric Explorert használjuk.

Az előtértároló feladatátvételéhez hajtsa végre a következő lépéseket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Kattintson a **fabric:/TestContainer/azurevotefront** csomópontra a fanézetben, és bontsa ki a partíciós csomópontot (egy GUID jelöli). Figyelje meg a csomópont nevét a fanézetben, amely azokat a csomópontokat mutatja, amelyeken a tároló jelenleg fut – például: `_nodetype_1`.
3. Bontsa ki a **Csomópontok** csomópontot a fanézetben. Kattintson a három pontra a tárolót futtató csomópont mellett.
4. Válassza az **Újraindítás** lehetőséget a csomópont újraindításához, majd erősítse meg az újraindítási műveletet. Az újraindítás kikényszeríti a tároló feladatátvételét a fürt egy másik csomópontjára.

![noderestart][noderestart]

Figyelje meg, hogyan módosul a csomópont neve (amely azt jelzi, hogy hol futnak az előtértárolók) egy másik csomópontra a fürtben. Néhány pillanat múlva ismét tallózhatja az alkalmazást, és láthatja, hogy az alkalmazás már egy másik csomóponton fut.

## <a name="scale-containers-and-services-in-a-cluster"></a>Tárolók és szolgáltatások méretezése a fürtökben

A Service Fabric-tárolók méretezhetők egy adott fürtben a szolgáltatások terhelésének megfelelően. A tárolók méretezése a fürtben futó példányok számának módosításával történik.

A webes előtér méretezéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Kattintson a három pontra a fanézetben a **fabric:/TestContainer/azurevotefront** csomópont mellett, és válassza a **Szolgáltatás méretezése** lehetőséget.

![sfxscale][sfxscale]

Most már méretezheti a webes előtér példányainak számát.

1. Módosítsa a számot **2**-re, és kattintson a **Szolgáltatás méretezése** gombra.
1. Kattintson a **fabric:/TestContainer/azurevotefront** csomópontra a fanézetben, és bontsa ki a partíciós csomópontot (egy GUID jelöli).

![sfxscaledone][sfxscaledone]

Most láthatja, hogy a szolgáltatás két példánnyal rendelkezik. A fanézetben megtekintheti, hogy a példányok melyik csomópontokon futnak.

Ezzel az egyszerű felügyeleti eljárással megdupláztuk az előtér-szolgáltatás számára a felhasználói terhelések feldolgozásához rendelkezésre álló erőforrások mennyiségét. Fontos megérteni, hogy nincs szükség több példányra ahhoz, hogy a szolgáltatás megbízhatóan fusson. Ha egy szolgáltatás meghibásodik, a Service Fabric gondoskodik róla, hogy egy új szolgáltatáspéldány kezdjen futni a fürtben.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a tároló feladatátvételét és egy alkalmazás méretezését mutattuk be. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * További tudnivalók a tárolók feladatátvételéről egy Service Fabric-fürtben
> * A webes előtértárolók méretezése egy alkalmazásban

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedett meg:
> [!div class="checklist"]
> * Tárolórendszerképek létrehozása
> * Tárolórendszerképek leküldése az Azure Container Registrybe
> * Tárolók csomagolása a Service Fabrichez a Yeoman használatával
> * Tárolókkal rendelkező Service Fabric-alkalmazás összeállítása és futtatása
> * A feladatátvétel és a méretezés kezelése a Service Fabricben

[noderestart]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialnoderestart.png
[sfxscale]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscale.png
[sfxscaledone]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscaledone.png