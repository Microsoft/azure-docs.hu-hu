---
title: CI/CD az Azure API Management ARM-sablonok használatával
description: Bevezetés az Azure API Management API-DevOps, Azure Resource Manager sablonokkal az API-telepítések CI/CD-folyamatban való kezeléséhez
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: 62f163b9ce649cd5ddb52b4325682570633dfb92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96183158"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>CI/CD API Management Azure Resource Manager sablonok használatával

Ebből a cikkből megtudhatja, hogyan használhatja az API-DevOps az Azure API Management Azure Resource Manager-sablonokon keresztül. Az API-k stratégiai értékével a folyamatos integráció és a folyamatos üzembe helyezés (CI/CD) folyamat az API-fejlesztés fontos aspektusa lesz. Lehetővé teszi a szervezetek számára, hogy automatizálják az API-módosításokat a hibákra vonatkozó manuális lépések nélkül, a korábbi problémákat észlelve, és végső soron a felhasználók számára gyorsabban nyújtsanak értéket. 

Az ebben a cikkben ismertetett DevOps megközelítés megvalósításához szükséges részleteket, eszközöket és kódrészleteket a GitHubon, a nyílt forráskódú [Azure API Management DevOps Resource Kit csomagban](https://github.com/Azure/azure-api-management-devops-resource-kit) találja. Mivel az ügyfelek számos mérnöki kultúrát és meglévő Automation-megoldást kínálnak, a megközelítés nem egy egyméretű megoldás.

## <a name="the-problem"></a>A probléma

A szervezetek manapság általában több üzembe helyezési környezettel rendelkeznek (például fejlesztési, tesztelési és éles környezetben), és az egyes környezetekhez külön API Management példányokat használnak. Egyes példányokat több fejlesztői csapat is megoszt, akik a különböző kiadási ritmusokkal rendelkező különböző API-kkal felelősek.

Ennek eredményeképpen az ügyfelek az alábbi kihívásokkal szembesülnek:

* API-k üzembe helyezésének automatizálása API Management
* Konfigurációk migrálása egyik környezetből a másikba
* Hogyan lehet elkerülni a különböző fejlesztői csapatok közötti interferenciát, amelyek ugyanazt a API Management példányt használják

## <a name="manage-configurations-in-resource-manager-templates"></a>Konfigurációk kezelése Resource Manager-sablonokban

A következő ábra a javasolt megközelítést mutatja be. 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="Diagram, amely a DevOps mutatja API Management.":::

Ebben a példában két központi telepítési környezet létezik: a *fejlesztés* és a *gyártás*. Mindegyiknek saját API Management példánya van. 

* Az API-fejlesztők hozzáférhetnek a fejlesztési példányhoz, és használhatják az API-k fejlesztéséhez és teszteléséhez. 
* Az *API-közzétevők* nevű kijelölt csapat kezeli az üzemi példányt.

Ennek a javasolt megközelítésnek a kulcsa az összes API Management konfiguráció megtartása [Azure Resource Manager-sablonokban](../azure-resource-manager/templates/template-syntax.md). A szervezetnek meg kell őriznie ezeket a sablonokat egy verziókövetés-rendszeren, például a git-ben. Ahogy az ábrán látható, a közzétevő adattár az éles API Management példány összes konfigurációját tartalmazza sablonok gyűjteményében:

|Sablon  |Leírás  |
|---------|---------|
|Szolgáltatássablon     | A API Management példány szolgáltatási szintjének konfigurációi, például az árképzési szint és az egyéni tartományok.         |
|Megosztott sablonok     |  A API Management-példányok, például a csoportok, a termékek és a naplózók közös erőforrásai.    |
|API-sablonok     |  API-k és alerőforrásaik konfigurációja: műveletek, házirendek, diagnosztikai beállítások.        |
|Főkiszolgáló (fő) sablonja     |   A minden sablonhoz való csatolással és az azok sorrendjében történő üzembe helyezésével [összekapcsolja](../azure-resource-manager/templates/linked-templates.md) a kapcsolatot. Ha az összes konfigurációt egy API Management példányra szeretné telepíteni, telepítse a fő sablont. Az egyes sablonokat egyenként is üzembe helyezheti.       |

Az API-fejlesztők a közzétevő tárházat a fejlesztői tárházba fogják lefoglalni, és az API-k változásain dolgoznak. A legtöbb esetben az API-sablonokra összpontosítanak, és nem kell módosítaniuk a megosztott vagy a szolgáltatási sablonokat.

## <a name="migrate-configurations-to-templates"></a>Konfigurációk migrálása sablonokba
Az API-fejlesztők szembesülnek a Resource Manager-sablonokkal való együttműködés kihívásaival:

* Az API-fejlesztők gyakran működnek a [OpenAPI-specifikációval](https://github.com/OAI/OpenAPI-Specification) , és előfordulhat, hogy nem ismerik a Resource Manager-sémákat. Előfordulhat, hogy az authoring templates manuálisan hiba okozta. 

   Az erőforrás-készlet [Creator](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) nevű eszköze segít automatizálni az API-sablonok létrehozását egy Open API-specifikációs fájl alapján. Emellett a fejlesztők API Management házirendeket is megadhatnak egy XML-formátumú API-hoz. 

* Azon ügyfelek esetében, akik már használnak API Management, egy másik kihívás a meglévő konfigurációk Resource Manager-sablonokba való kinyerése. Ezen ügyfelek esetében az erőforrás-készletben a [Extractor](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) nevű eszköz segíthet a sablonok létrehozásában a konfigurációk API Management példányokból való kibontásával.  

## <a name="workflow"></a>Munkafolyamat

* Miután az API-fejlesztők befejezték az API-k fejlesztését és tesztelését, és létrehozták az API-sablonokat, lekéréses kérelmeket küldhetnek a közzétevő tárházban lévő módosítások egyesítéséhez. 

* Az API-közzétevők ellenőrizhetik a lekéréses kérelmeket, és ellenőrizhetik, hogy a módosítások biztonságosak és megfelelőek-e. Megvizsgálhatja például, hogy csak HTTPS-kommunikáció engedélyezett-e az API-val. A legtöbb érvényesítés automatizálható a CI/CD-folyamat lépéseit követve.

* Ha a módosítások jóváhagyása és egyesítése sikeresen megtörtént, az API-közzétevők dönthetnek úgy, hogy az éles példányra vagy igény szerint telepítik őket. A sablonok üzembe helyezése a [GitHub-műveletek](https://github.com/Azure/apimanagement-devops-samples), az [Azure-folyamatok](/azure/devops/pipelines), a [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md), az [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)vagy más eszközök használatával automatizálható.


Ezzel a módszerrel a szervezet automatizálhatja az API-változások API Management példányokra való telepítését, és megkönnyíti az egyik környezetből a másikba való átalakítását. Mivel a különböző API-fejlesztési csapatok az API-sablonok és-fájlok különböző csoportjain dolgoznak, megakadályozza a különböző csapatok közötti interferenciát.

## <a name="video"></a>Videó

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>Következő lépések

- További információkat, eszközöket és példákat a nyílt forráskódú [Azure API Management DevOps Resource Kit csomagban](https://github.com/Azure/azure-api-management-devops-resource-kit) talál.