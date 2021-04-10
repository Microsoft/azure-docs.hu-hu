---
title: Indítási feladatok futtatása az Azure Cloud Services (klasszikus) | Microsoft Docs
description: Az indítási feladatok segítséget nyújtanak a felhőalapú szolgáltatási környezet előkészítésében az alkalmazáshoz. Ez a cikk bemutatja, hogyan működnek az indítási feladatok, és hogyan lehet őket
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 25190075bdd13bd4b75dd82c97ee06ee60f4c26c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743185"
---
# <a name="how-to-configure-and-run-startup-tasks-for-an-azure-cloud-service-classic"></a>Azure Cloud Service indítási feladatainak konfigurálása és futtatása (klasszikus)

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Az indítási feladatokkal műveleteket hajthat végre a szerepkörök elkezdése előtt. A végrehajtani kívánt műveletek közé tartozik például az összetevők telepítése, a COM-összetevők regisztrálása, a beállításkulcsok beállítása vagy a hosszú ideig futó folyamat elindítása.

> [!NOTE]
> Az indítási feladatok nem alkalmazhatók Virtual Machinesra, csak a Cloud Service webes és feldolgozói szerepköreire.
> 
> 

## <a name="how-startup-tasks-work"></a>Az indítási feladatok működése
Az indítási feladatok olyan műveletek, amelyek a szerepkörök megkezdése előtt lettek végrehajtva, és a [ServiceDefinition. csdef] fájlban vannak meghatározva az [indítási] elemben található [Task] elem használatával. A gyakran használt indítási feladatok batch-fájlok, de a konzolon futó alkalmazások vagy a PowerShell-parancsfájlokat indító batch-fájlok is lehetnek.

A környezeti változók egy indítási feladatba adják át az adatokat, a helyi tárterület pedig az indítási feladatokból származó információk továbbítására használható. Egy környezeti változó például megadhatja a telepíteni kívánt program elérési útját, és a fájlok megírhatók a helyi tárolóba, amelyet később a szerepkörök is elolvashatnak.

Az indítási feladat adatokat és hibákat tud naplózni a **temp** környezeti változó által megadott könyvtárba. Az indítási feladat során a **temp** környezeti változó a *C: \\ Resources \\ temp \\ [GUID] értékre van feloldva. [ rolename] \\ RoleTemp* -címtár a felhőben való futtatáskor.

Az indítási feladatok két újraindítás között többször is végrehajthatók. Például az indítási feladat a szerepkör minden egyes újraindításakor fut, és a szerepkör-újraindítás pedig nem feltétlenül jár újraindítással. Az indítási feladatokat úgy kell megírni, hogy a több alkalommal is fussanak a problémák nélkül.

Az indítási feladatoknak az **indítási** folyamat befejezéséhez nullával (vagy kilépési kóddal) kell végződnie. Ha egy indítási feladat nullától eltérő **errorlevel**-értékre végződik, a szerepkör nem indul el.

## <a name="role-startup-order"></a>Szerepkör indítási sorrendje
Az alábbi listában az Azure-beli szerepkör indítási eljárása látható:

1. A példány **kezdőként** van megjelölve, és nem kap forgalmat.
2. Az összes indítási feladat a **taskType** attribútum szerint lesz végrehajtva.
   
   * Az **egyszerű** feladatok szinkron módon, egyszerre lesznek végrehajtva.
   * A **háttér** -és **előtér** -feladatok aszinkron módon, az indítási feladattal párhuzamosan indíthatók el.  
     
     > [!WARNING]
     > Előfordulhat, hogy az IIS-t nem lehet teljesen konfigurálni az indítási folyamat indításakor, így előfordulhat, hogy a szerepkör-specifikus adatok nem érhetők el. A szerepkör-specifikus adatokhoz szükséges indítási feladatoknak a [Microsoft. WindowsAzure. ServiceRuntime. RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100))használatát kell használniuk.
     > 
     > 
3. A szerepkör-gazdagép folyamata elindult, és a hely az IIS-ben jön létre.
4. A rendszer a [Microsoft. WindowsAzure. ServiceRuntime. RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) metódust hívja meg.
5. A példány **készként** van megjelölve, és a rendszer átirányítja a forgalmat a példányhoz.
6. A rendszer a [Microsoft. WindowsAzure. ServiceRuntime. RoleEntryPoint. Run](/previous-versions/azure/reference/ee772746(v=azure.100)) metódust hívja meg.

## <a name="example-of-a-startup-task"></a>Indítási feladat – példa
Az indítási feladatok a [ServiceDefinition. csdef] fájlban vannak definiálva a **Task** elemben. A **commandLine** attribútum megadja az indítási batch-fájl vagy a konzol parancsának nevét és paramétereit, a **executionContext** attribútum meghatározza az indítási feladat jogosultsági szintjét, és a **taskType** attribútum meghatározza a feladat végrehajtásának módját.

Ebben a példában egy **MyVersionNumber** nevű környezeti változó jön létre az indítási feladathoz, és a "**1.0.0.0**" értékre van állítva.

**ServiceDefinition. csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

A következő példában a **Startup. cmd** batch-fájl az "aktuális verzió 1.0.0.0" sort írja a StartupLog.txt fájlba a TEMP környezeti változó által megadott könyvtárban. A `EXIT /B 0` sor biztosítja, hogy az indítási tevékenység nulla **errorlevel** értékkel végződik.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> A Visual Studióban az indítási batch-fájl **Másolás a kimeneti könyvtárba** tulajdonságának úgy kell beállítania, hogy **mindig a másoláskor** ellenőrizze, hogy az indítási batch-fájl megfelelően van-e telepítve a projekthez az Azure-ban (**AppRoot \\ bin** webes szerepkörökhöz, és **AppRoot** a feldolgozói szerepkörökhöz).
> 
> 

## <a name="description-of-task-attributes"></a>Feladatattribútumok leírása
Az alábbiakban a [ServiceDefinition. csdef] fájlban található **Task** elem attribútumait ismertetjük:

**commandLine** – az indítási feladat parancssorát adja meg:

* A parancs a parancssori paraméterekkel, amely megkezdi az indítási feladatot.
* Gyakran ez egy. cmd vagy. bat nevű batch-fájl fájlneve.
* A feladat az üzemelő példány AppRoot \\ bin mappájához képest. A környezeti változók nincsenek kibontva a feladat elérési útjának és fájljának meghatározásához. Ha a környezet bővítése kötelező, létrehozhat egy kis. cmd parancsfájlt, amely meghívja az indítási feladatot.
* Lehet egy konzolos alkalmazás vagy egy olyan batch-fájl, amely egy [PowerShell-parancsfájlt](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)indít el.

**executionContext** – megadja az indítási feladat jogosultsági szintjét. A jogosultsági szint korlátozott vagy emelt szintű lehet:

* **korlátozott**  
  Az indítási feladat ugyanazokkal a jogosultságokkal fut, mint a szerepkör. Ha a [futásidejű] elem **executionContext** attribútuma is **korlátozott**, a rendszer a felhasználói jogosultságokat használja.
* **emelt szintű**  
  Az indítási feladat rendszergazdai jogosultságokkal fut. Ez lehetővé teszi a programok telepítésének indítási feladatait, az IIS konfigurációjának módosítását, a beállításjegyzék módosítását és egyéb rendszergazdai szintű feladatokat a szerepkör jogosultsági szintjének növelése nélkül.  

> [!NOTE]
> Az indítási feladatokhoz tartozó jogosultsági szintnek nem kell megegyeznie a szerepkörrel.
> 
> 

**taskType** – az indítási feladat végrehajtásának módját adja meg.

* **egyszerű**  
  A feladatok szinkron módon, egyenként, a [ServiceDefinition. csdef] fájlban megadott sorrendben hajthatók végre. Ha egy **egyszerű** indítási feladat nulla **errorlevel** értékkel végződik, a rendszer a következő **egyszerű** indítási feladatot hajtja végre. Ha nincs több **egyszerű** indítási feladat, akkor a szerepkör maga lesz elindítva.   
  
  > [!NOTE]
  > Ha az **egyszerű** feladat nullától eltérő **errorlevel**-értékre végződik, a példány le lesz tiltva. Az ezt követő **egyszerű** indítási feladatok és a szerepkör maga a művelet nem indul el.
  > 
  > 
  
    Annak biztosításához, hogy a batch-fájl  nullával végződik, futtassa a parancsot `EXIT /B 0` a batch-fájl folyamat végén.
* **háttér**  
  A feladatok aszinkron módon, a szerepkör indításakor párhuzamosan hajthatók végre.
* **előtér**  
  A feladatok aszinkron módon, a szerepkör indításakor párhuzamosan hajthatók végre. Az **előtér** és a **háttérben** futó feladat közötti fő különbség az, hogy az **előtéri** feladat megakadályozza a szerepkör újrahasznosítását vagy leállítását, amíg a feladat véget nem ér. A **háttérben futó** feladatok nem rendelkeznek ezzel a korlátozással.

## <a name="environment-variables"></a>Környezeti változók
A környezeti változók segítségével adatokat adhat át egy indítási feladatnak. Tegyük fel például, hogy az elérési utat egy olyan blobba helyezi, amely a telepítendő programot vagy a szerepkör által használt portszámokat, illetve az indítási feladat funkcióinak vezérlésére szolgáló beállításokat tartalmaz.

Az indítási feladatokhoz kétféle környezeti változó létezik; statikus környezeti változók és környezeti változók a [RoleEnvironment] osztály tagjai alapján. Mindkettő a [ServiceDefinition. csdef] fájl [környezet] szakaszában található, és mindkettő a [változó] elemet és a **Name** attribútumot használja.

A statikus környezeti változók a [változó] elem **Value** attribútumát használják. A fenti példa létrehozza a **MyVersionNumber** környezeti változót, amely a "**1.0.0.0**" statikus értékkel rendelkezik. Egy másik példa egy **StagingOrProduction** környezeti változó létrehozása, amely manuálisan beállítható "**átmeneti**" vagy "**éles**" értékre, ha a **StagingOrProduction** környezeti változó értéke alapján különböző indítási műveleteket hajt végre.

A RoleEnvironment osztály tagjai alapján a környezeti változók nem a [változó] elem **Value** attribútumát használják. Ehelyett a [RoleInstanceValue] gyermek elemnek a megfelelő **XPath** -attribútum értékkel kell rendelkeznie a környezeti változók létrehozásához a [RoleEnvironment] osztály egy adott tagja alapján. A különböző [RoleEnvironment] -értékek eléréséhez használt **XPath** attribútum értékei [itt](cloud-services-role-config-xpath.md)találhatók.

Ha például egy "**igaz**" nevű környezeti változót szeretne létrehozni, ha a példány a Compute Emulatorban fut, a "**false**" pedig a felhőben való futtatáskor, használja a következő [változó] -és [RoleInstanceValue] elemeket:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Következő lépések
Ismerje meg, hogyan hajthat végre [gyakori indítási feladatokat](cloud-services-startup-tasks-common.md) a felhőalapú szolgáltatásban.

A felhőalapú szolgáltatás [becsomagolása](cloud-services-model-and-package.md) .  

[ServiceDefinition. csdef]: cloud-services-model-and-package.md#csdef
[Feladat]: /previous-versions/azure/reference/gg557552(v=azure.100)#Task
[Indítás]: /previous-versions/azure/reference/gg557552(v=azure.100)#Startup
[Futtatókörnyezet]: /previous-versions/azure/reference/gg557552(v=azure.100)#Runtime
[Environment]: /previous-versions/azure/reference/gg557552(v=azure.100)#Environment
[Változó]: /previous-versions/azure/reference/gg557552(v=azure.100)#Variable
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue
[RoleEnvironment]: /previous-versions/azure/reference/ee773173(v=azure.100)