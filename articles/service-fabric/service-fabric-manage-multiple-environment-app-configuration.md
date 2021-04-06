---
title: Alkalmazások kezelése több környezethez
description: Az Azure Service Fabric-alkalmazások olyan fürtökön is futtathatók, amelyek mérete egy gépről több ezer gépre terjed ki. Bizonyos esetekben a különböző környezetekben különbözőképpen kell konfigurálnia az alkalmazást. Ez a cikk bemutatja, hogyan határozhat meg különböző alkalmazási paramétereket környezetekben.
ms.topic: conceptual
ms.date: 02/23/2018
ms.openlocfilehash: c907540c03788ab5f4087a96e301f18ab7ced4ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98787977"
---
# <a name="manage-applications-for-multiple-environments"></a>Alkalmazások kezelése több környezethez

Az Azure Service Fabric-fürtök lehetővé teszik fürtök létrehozását egy vagy több ezer gép között bárhol. A legtöbb esetben azt látja, hogy az alkalmazást több fürtkonfiguráció között kell üzembe helyeznie: a helyi fejlesztési fürttel, egy megosztott fejlesztési fürttel és az üzemi fürttel. Az összes ilyen fürt más környezetnek minősül, amelyet a kódnak futtatnia kell. Az alkalmazás bináris fájljai ezen a széles spektrumon való módosítás nélkül is futtathatók, de általában másképpen kell konfigurálni az alkalmazást.

Vegyünk két egyszerű példát:
  - a szolgáltatás egy meghatározott porton figyel, de a portnak a környezetek között eltérőnek kell lennie
  - különböző kötési hitelesítő adatokat kell megadnia egy adatbázishoz a környezetek között

## <a name="specifying-configuration"></a>Konfiguráció meghatározása

Az Ön által megadott konfiguráció két kategóriába osztható:

- A szolgáltatások futtatásának módjára vonatkozó konfiguráció
  - Például egy végpont portszáma vagy egy szolgáltatás példányainak száma
  - Ez a konfiguráció az alkalmazás vagy szolgáltatás jegyzékfájljában van megadva.
- Az alkalmazás kódjára érvényes konfiguráció
  - Például egy adatbázishoz tartozó kötési információ
  - Ez a konfiguráció konfigurációs fájlokon vagy környezeti változókon keresztül is biztosítható

> [!NOTE]
> Nem minden attribútum szerepel az alkalmazás-és szolgáltatás jegyzékfájl-fájljában.
> Ezekben az esetekben a karakterláncokat a telepítési munkafolyamat részeként kell kicserélni. Az Azure DevOps olyan bővítményeket használhat, mint például a tokenek cseréje: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens vagy a Jenkins-ben egy parancsfájl-feladat futtatásával lecserélheti az értékeket.
>

## <a name="specifying-parameters-during-application-creation"></a>Paraméterek megadása az alkalmazás létrehozása során

Elnevezett alkalmazás-példányok Service Fabricban való létrehozásakor lehetősége van átadni a paramétereket. Ennek módja attól függ, hogyan hozza létre az alkalmazás példányát.

  - A PowerShellben a [`New-ServiceFabricApplication`](/powershell/module/servicefabric/new-servicefabricapplication) parancsmag szórótábla-ként veszi át az alkalmazás paramétereit.
  - A sfctl használatakor a [`sfctl application create`](./service-fabric-sfctl-application.md#sfctl-application-create) parancs JSON-karakterláncként fogadja a paramétereket. A install.sh parancsfájl a sfctl-t használja.
  - A Visual Studio az alkalmazás projekt parameters (paraméterek) mappájában található paramétereket tartalmazó fájlok készletét biztosítja. Ezeket a paramétereket a Visual studióból való közzétételkor, az Azure DevOps Services vagy a Azure DevOps Server használatával lehet használni. A Visual Studióban a paramétereket a rendszer a Deploy-FabricApplication.ps1 parancsfájlba továbbítja.

## <a name="next-steps"></a>Következő lépések
A következő cikkek bemutatják, hogyan használhatja az itt ismertetett fogalmakat:

- [Környezeti változók megadása a Service Fabric szolgáltatásaihoz](service-fabric-how-to-specify-environment-variables.md)
- [Szolgáltatás portszámának megadása a Service Fabric paramétereinek használatával](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Konfigurációs fájlok parametrizálja](service-fabric-how-to-parameterize-configuration-files.md)

- [Környezeti változó referenciája](service-fabric-environment-variables-reference.md)
