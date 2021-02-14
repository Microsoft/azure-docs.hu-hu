---
title: Felügyeleti központ
description: A kapcsolatok kezelése, a verziókövetés konfigurációja és a globális szerzői tulajdonságok a Azure Data Factory felügyeleti központban
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.date: 02/01/2021
ms.openlocfilehash: b4b9ecef84f8ffcc82107299ad6603466380d1c0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371498"
---
# <a name="management-hub-in-azure-data-factory"></a>Felügyeleti központ Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory UX *felügyelet* lapján elérhető felügyeleti központ egy olyan portál, amely globális felügyeleti műveleteket üzemeltet az Ön adatelőállítójának. Itt kezelheti az adattárakhoz és a külső számításokhoz, a verziókövetés konfigurációjához és az aktiválási beállításokhoz kapcsolódó kapcsolatokat.

## <a name="manage-connections"></a>Kapcsolatok kezelése

### <a name="linked-services"></a>Társított szolgáltatások

A társított szolgáltatások határozzák meg Azure Data Factory kapcsolódási adatait külső adattárakhoz és számítási környezetekhez való kapcsolódáshoz. További információ: [társított szolgáltatások – fogalmak](concepts-linked-services.md). A társított szolgáltatás létrehozása, szerkesztése és törlése a felügyeleti központban történik.

![Társított szolgáltatások kezelése](media/author-management-hub/management-hub-linked-services.png)

### <a name="integration-runtimes"></a>Integrációs modulok

Az integrációs modul egy Azure Data Factory által használt számítási infrastruktúra, amely különböző hálózati környezetekben biztosít adatintegrációs képességeket. További információ az [Integration Runtime alapfogalmait](concepts-integration-runtime.md)ismertető témakörben. A felügyeleti központban hozhatja létre, törölheti és figyelheti az integrációs modulokat.

![Integrációs modulok kezelése](media/author-management-hub/management-hub-integration-runtime.png)

## <a name="manage-source-control"></a>Verziókövetés kezelése

### <a name="git-configuration"></a>Git-konfiguráció

A git-vel kapcsolatos összes információt megtekintheti/szerkesztheti a felügyeleti központ git-konfigurációs beállításai között. 

A legutóbb közzétett véglegesítő információk is fel vannak sorolva, és segíthetnek megérteni a pontos véglegesítés, amelyet legutóbb közzétettek és telepítettek a környezetek között. Emellett hasznos lehet, ha éles javításokat végez a környezetben.

További információért olvassa [el a Azure Data Factory a verziókövetés](source-control.md)című témakört.

![Git-tárház kezelése](media/author-management-hub/management-hub-git.png)

### <a name="parameterization-template"></a>Paraméterezés-sablon

Ha szeretné felülbírálni a létrehozott Resource Manager-sablon paramétereit az együttműködési ág közzétételekor, létrehozhat vagy szerkeszthet egyéni paramétereket tartalmazó fájlt. További információért olvassa el [az egyéni paraméterek használata a Resource Manager-sablonban](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template)című témakört. A paraméterezés-sablon csak git-tárházban végzett munka esetén érhető el. Ha a *arm-template-parameters-definition.js* fájl nem létezik a munkaterületen, az alapértelmezett sablon szerkesztésével létrejön.

![Egyéni paraméterek kezelése](media/author-management-hub/management-hub-custom-parameters.png)

## <a name="manage-authoring"></a>Szerzői műveletek kezelése

### <a name="triggers"></a>Triggerek

Az eseményindítók határozzák meg, hogy a folyamat mikor fusson. Az eseményindítók jelenleg egy falióra-ütemezéssel, rendszeres időközönként működnek, vagy egy eseménytől függenek. További információért olvassa el az [trigger-végrehajtást](concepts-pipeline-execution-triggers.md#trigger-execution)ismertető témakört. A felügyeleti központban létrehozhat, szerkeszthet, törölhet vagy megtekintheti az trigger aktuális állapotát.

![Képernyőkép, amely bemutatja, hol lehet létrehozni, szerkeszteni, törölni vagy megtekinteni egy trigger aktuális állapotát.](media/author-management-hub/management-hub-triggers.png)

### <a name="global-parameters"></a>Globális paraméterek

A globális paraméterek állandók egy olyan adatelőállítón keresztül, amelyet bármely kifejezésben felhasználhat egy folyamat. További információért olvassa el a [globális paramétereket](author-global-parameters.md).

![Globális paraméterek létrehozása](media/author-global-parameters/create-global-parameter-3.png)

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [konfigurálhat git-tárházat](source-control.md) az ADF-hez


