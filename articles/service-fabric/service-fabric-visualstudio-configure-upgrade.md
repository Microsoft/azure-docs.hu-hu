---
title: Service Fabric alkalmazás frissítésének konfigurálása
description: Megtudhatja, hogyan konfigurálhatja a Service Fabric alkalmazás frissítésének beállításait a Microsoft Visual Studio használatával.
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: 11df0387db07669ebacdba2e3d850e32903da133
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96573680"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Service Fabric alkalmazás frissítésének konfigurálása a Visual Studióban
Az Azure-hoz készült Visual Studio Tools Service Fabric biztosítanak a helyi vagy távoli fürtökre való közzétételhez szükséges frissítési támogatást. Az alkalmazásnak a tesztelés és a hibakeresés során való lecserélése helyett három forgatókönyvvel kell frissítenie az alkalmazást egy újabb verzióra:

* Az alkalmazásadatok nem vesznek el a frissítés során.
* A rendelkezésre állás magas marad, így a frissítés során a szolgáltatás nem szakad meg, ha elegendő szolgáltatási példány van elosztva a frissítési tartományok között.
* A tesztek futtathatók egy alkalmazáson, amíg a frissítés folyamatban van.

## <a name="parameters-needed-to-upgrade"></a>A frissítéshez szükséges paraméterek
Két telepítési típus közül választhat: normál vagy frissítés. A normál telepítés törli az összes korábbi központi telepítési információt és adatot a fürtön, míg a frissítési központi telepítés megőrzi azt. Amikor egy Service Fabric alkalmazást frissít a Visual Studióban, meg kell adnia az alkalmazás frissítési paramétereit és az állapot-ellenőrzési házirendeket. Az alkalmazás-frissítési paraméterek segítenek a frissítés szabályozásában, míg az állapot-ellenőrzési házirendek határozzák meg, hogy a frissítés sikeres volt-e. További részletekért tekintse meg [Service Fabric alkalmazás frissítése: frissítési paraméterek](service-fabric-application-upgrade-parameters.md) című témakört.

Három frissítési mód van: *figyelt*, *UnmonitoredAuto* és *UnmonitoredManual*.

* A figyelt frissítés automatizálja a frissítés és az alkalmazás állapotának ellenőrzését.
* Egy UnmonitoredAuto-frissítés automatizálja a frissítést, de kihagyja az alkalmazás állapotának ellenőrzését.
* Ha UnmonitoredManual-frissítést végez, manuálisan kell frissítenie az egyes frissítési tartományokat.

Minden frissítési módnak különböző paramétereket kell megadnia. Az elérhető frissítési lehetőségekkel kapcsolatos további tudnivalókért tekintse meg az [alkalmazás frissítési paramétereit](service-fabric-application-upgrade-parameters.md) .

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Service Fabric alkalmazás frissítése a Visual Studióban
Ha a Visual Studio Service Fabric eszközeit használja egy Service Fabric alkalmazás frissítésére, akkor az **alkalmazás frissítése** jelölőnégyzet bejelölésével megadhat egy frissítésre való közzétételi folyamatot, amely nem normál telepítés.

### <a name="to-configure-the-upgrade-parameters"></a>A frissítési paraméterek konfigurálása
1. Kattintson a jelölőnégyzet melletti **Beállítások** gombra. Megjelenik a **frissítési paraméterek szerkesztése** párbeszédpanel. A **frissítési paraméterek szerkesztése** párbeszédpanel támogatja a figyelt, a UnmonitoredAuto és a UnmonitoredManual frissítési módjait.
2. Válassza ki a használni kívánt frissítési módot, majd töltse ki a paraméter rácsát.

    Minden paraméternek van alapértelmezett értéke. A nem kötelező paraméter *DefaultServiceTypeHealthPolicy* -bemenetet vesz fel. Íme egy példa a kivonatoló táblázat bemeneti formátumára a *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    A *ServiceTypeHealthPolicyMap* egy másik opcionális paraméter, amely a kivonatoló tábla bemenetét a következő formátumban veszi figyelembe:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Itt egy valós példa:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Ha a UnmonitoredManual frissítési módot választja, akkor a folytatáshoz manuálisan kell elindítania a PowerShell-konzolt, és be kell fejeznie a frissítési folyamatot. A manuális frissítés működésének megismeréséhez tekintse meg az [Service Fabric alkalmazás frissítése: speciális témaköröket](service-fabric-application-upgrade-advanced.md) .

## <a name="upgrade-an-application-by-using-powershell"></a>Alkalmazás frissítése a PowerShell használatával
Service Fabric alkalmazások frissítéséhez PowerShell-parancsmagokat használhat. Részletes információkért lásd: [Service Fabric alkalmazás verziófrissítésének oktatóanyaga](service-fabric-application-upgrade-tutorial.md) és a [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) .

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Állapot-ellenőrzési házirend meghatározása az alkalmazás jegyzékfájljában
Egy Service Fabric alkalmazás minden szolgáltatása rendelkezhet saját állapotházirend-paraméterekkel, amelyek felülbírálják az alapértelmezett értékeket. Ezeket a paramétereket megadhatja az alkalmazás jegyzékfájljában.

Az alábbi példa bemutatja, hogyan alkalmazhat egyedi állapot-ellenőrzési házirendet az alkalmazás jegyzékfájljában lévő egyes szolgáltatásokhoz.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Következő lépések
Az alkalmazások frissítésével kapcsolatos további információkért lásd: [alkalmazások frissítése a Visual Studióval](service-fabric-application-upgrade-tutorial.md).
