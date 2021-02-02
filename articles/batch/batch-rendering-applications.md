---
title: Alkalmazások renderelése
description: Azure Batch használatával bármilyen renderelési alkalmazás használható. Az Azure Marketplace virtuálisgép-lemezképei azonban előre telepített általános alkalmazásokkal is elérhetők.
ms.date: 02/01/2021
ms.topic: how-to
ms.openlocfilehash: 4eeb87c866858f2f1733b81e3e2154f4521e94bc
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430028"
---
# <a name="pre-installed-applications-on-batch-rendering-vm-images"></a>Előre telepített alkalmazások a Batch rendering virtuálisgép-rendszerképein

Azure Batch használatával bármilyen renderelési alkalmazás használható. Az Azure Marketplace virtuálisgép-lemezképei azonban előre telepített általános alkalmazásokkal is elérhetők.

Ha alkalmazható, a használaton kívüli licencelés elérhető az előre telepített renderelési alkalmazásokhoz. Batch-készlet létrehozásakor a szükséges alkalmazások megadhatók, és a virtuális gép és az alkalmazások díja percenként történik. Az alkalmazások árai a [Azure batch díjszabási oldalon](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)találhatók.

Néhány alkalmazás csak a Windowst támogatja, de a legtöbb Windows és Linux rendszeren is támogatott.

## <a name="applications-on-centos-7-rendering-image"></a>A CentOS 7 renderelési képét használó alkalmazások

A következő lista a CentOS 7,6-es verziójú 1.1.6-renderelési lemezképekre vonatkozik.

* Autodesk Maya I/O 2017 5. frissítés (201708032230-s verzió)
* Autodesk Maya I/O 2018 2. frissítés (cut 201711281015)
* Autodesk Maya I/O 2019 1. frissítés
* Autodesk Arnold Maya 2017 (Arnold Version 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold Maya 2018 (Arnold Version 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold Maya 2019 (Arnold Version 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (3.60.04-es verzió)
* Chaos Group V-Ray for Maya 2018 (3.60.04-es verzió)
* Blender (2.68)
* Turmixgép (2,8)

## <a name="applications-on-latest-windows-server-rendering-image"></a>A Windows Server legújabb megjelenítési lemezképét bemutató alkalmazások

A következő lista a Windows Server renderelési rendszerképére, a 1.5.0-es verzióra vonatkozik.

* Autodesk Maya I/O 2020 frissítés 4,4
* Autodesk 3ds Max I/O 2021 3. frissítés
* Autodesk Arnold Maya 2020 (Arnold Version 6.1.0.1) MtoA-4.1.1.1-2020
* Autodesk Arnold a 3ds Max 2021 (Arnold Version 6.1.0.1) MAXtoA-4.2.2.20-2021
* Chaos Group V-Ray a Maya 2020 (5.00.21 verzió)
* Chaos Group V-Ray a 3ds Max 2021 (5.00.05 verzió)
* Blender (2.79)
* Turmixgép (2,80)
* AZ 10

> [!IMPORTANT]
> A V-Ray és a Maya [Azure batch-bővítmény sablonjain](https://github.com/Azure/batch-extension-templates)kívüli futtatásához kezdje `vrayses.exe` a renderelés futtatása előtt. A sablonokon kívüli vrayses.exe elindításához használhatja a következő parancsot `%MAYA_2020%\vray\bin\vrayses.exe"` .
>
> Példaként tekintse meg a [Maya és a V-Ray sablon](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) indítási feladatát a githubon.

## <a name="applications-on-previous-windows-server-rendering-images"></a>A Windows Server korábbi rendszerképein futó alkalmazások

A következő lista a Windows Server 2016-es verziójának 1.3.8-megjelenítési lemezképeit érinti.

* Autodesk Maya I/O 2017 5. frissítés (17.4.5459-es verzió)
* Autodesk Maya I/O 2018 6. frissítés (18.4.0.7622 verzió)
* Autodesk Maya I/O 2019
* Autodesk 3ds Max I/O 2018 4. frissítés (20.4.0.4254-es verzió)
* Autodesk 3ds Max I/O 2019 1. frissítés (21.2.0.2219 verzió)
* Autodesk 3ds Max I/O 2020 2. frissítés
* Autodesk Arnold Maya 2017 (Arnold Version 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold Maya 2018 (Arnold Version 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold Maya 2019 (Arnold Version 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold 3ds Max 2018 (Arnold Version 5.3.0.2) (1.2.926 verzió)
* Autodesk Arnold 3ds Max 2019 (Arnold Version 5.3.0.2) (1.2.926 verzió)
* Autodesk Arnold 3ds Max 2020 (Arnold Version 5.3.0.2) (1.2.926 verzió)
* Chaos Group V-Ray a Maya 2017 (4.12.01 verzió)
* Chaos Group V-Ray a Maya 2018 (4.12.01 verzió)
* Chaos Group V-Ray a Maya 2019 (4.04.03 verzió)
* Chaos Group V-Ray a 3ds Max 2018 (4.20.01 verzió)
* Chaos Group V-Ray a 3ds Max 2019 (4.20.01 verzió)
* Chaos Group V-Ray a 3ds Max 2020 (4.20.01 verzió)
* Blender (2.79)
* Turmixgép (2,80)
* AZ 10

A következő lista a Windows Server 2016-es verziójának 1.3.7-megjelenítési lemezképeit érinti.

* Autodesk Maya I/O 2017 5. frissítés (17.4.5459-es verzió)
* Autodesk Maya I/O 2018 4. frissítés (18.4.0.7622 verzió)
* Autodesk 3ds Max I/O 2019 1. frissítés (21.2.0.2219 verzió)
* Autodesk 3ds Max I/O 2018 4. frissítés (20.4.0.4254-es verzió)
* Autodesk Arnold Maya 2017 (Arnold Version 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold Maya 2018 (Arnold Version 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold 3ds Max 2018 (Arnold Version 5.0.2.4) (1.2.926 verzió)
* Autodesk Arnold 3ds Max 2019 (Arnold Version 5.0.2.4) (1.2.926 verzió)
* Chaos Group V-Ray a Maya 2018 (3.52.03 verzió)
* Chaos Group V-Ray a 3ds Max 2018 (3.60.02 verzió)
* Chaos Group V-Ray a Maya 2019 (3.52.03 verzió)
* Chaos Group V-Ray a 3ds Max 2019 (4.10.01 verzió)
* Blender (2.79)

> [!NOTE]
> A Chaos-csoport V-Ray for 3ds Max 2019 (4.10.01 verzió) bevezeti a V-Ray változásait. Az előző verzió (3.60.02-verzió) használatához használja a Windows Server 2016-es verzióját, a 1.3.2 renderelési csomópontokat.

## <a name="next-steps"></a>Következő lépések

A renderelési virtuálisgép-lemezképek használatához a készlet létrehozásakor meg kell adni őket a készlet konfigurációjában. Tekintse [meg a Batch-készlet képességeit a rendereléshez](./batch-rendering-functionality.md).
