---
title: Forrás-hozzárendelési támogatás JavaScript-alkalmazásokhoz – Azure Monitor Application Insights
description: Megtudhatja, hogyan tölthet fel forrás térképeket a saját Storage-fiók blob-tárolójába Application Insights használatával.
ms.topic: conceptual
author: DavidCBerry13
ms.author: daberry
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 79462dcb503e5835254b89f7ebaa7a9ee34946d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933948"
---
# <a name="source-map-support-for-javascript-applications"></a>A JavaScript-alkalmazások forrás-hozzárendelési támogatása

A Application Insights támogatja a forrás-hozzárendelések saját Storage-fiók blob-tárolóra való feltöltését.
A forrás-és a végpontok a végpontok közötti tranzakció részletei lapon találhatók. A [JavaScript SDK][ApplicationInsights-JS] vagy a [Node.js SDK][ApplicationInsights-Node.js] által eljuttatott kivételek nem használhatók fel a forrás térképek használatával.

![A hívási verem kiépítésének megjelölése a Storage-fiókkal való összekapcsolással](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Új Storage-fiók és blob-tároló létrehozása

Ha már rendelkezik egy meglévő Storage-fiókkal vagy blob-tárolóval, kihagyhatja ezt a lépést.

1. [Új Storage-fiók létrehozása][create storage account]
2. [Hozzon létre egy BLOB-tárolót][create blob container] a Storage-fiókon belül. Győződjön meg arról, hogy a "nyilvános hozzáférési szint" értékre `Private` van állítva, hogy a forrás-térképek ne legyenek nyilvánosan elérhetők.

> [!div class="mx-imgBorder"]
>![A tároló hozzáférési szintjét privát értékre kell beállítani](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>A forrás leküldése a blob-tárolóba

Állítsa be a folyamatos üzembe helyezési folyamatot a Storage-fiókjába úgy, hogy úgy konfigurálja, hogy automatikusan feltöltse a forrás térképeket a beállított blob-tárolóba.

A forrás-leképezések feltölthetők a Blob Storage tárolóba ugyanazzal a mappastruktúrát, amelyet & üzembe helyezésével készítettek le. Gyakori használati eset a telepítési mappa előtagja a verziószámával, például: `1.2.3/static/js/main.js` . Amikor egy nevű Azure Blob-tárolón keresztül `sourcemaps` felveszi az egyiket, a rendszer megkísérli beolvasni a (z) helyen található forrás-hozzárendelést `sourcemaps/1.2.3/static/js/main.js.map` .

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Forrás térképek feltöltése az Azure-folyamatok használatával (ajánlott)

Ha Azure-folyamatokat használ az alkalmazás folyamatos kiépítéséhez és üzembe helyezéséhez, vegyen fel egy [Azure file Copy][azure file copy] feladatot a folyamatba, hogy automatikusan feltöltse a forrás térképeket.

> [!div class="mx-imgBorder"]
> ![Azure-fájlmásolás feladatának hozzáadása a folyamathoz a forrás térképek Azure-ba való feltöltéséhez Blob Storage](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>A Application Insights-erőforrás konfigurálása forrás-hozzárendelési fiókkal

### <a name="from-the-end-to-end-transaction-details-page"></a>A végpontok közötti tranzakció részletei lapon

A végpontok közötti tranzakció részletei lapon kattintson a *unkisebbít* parancsra, és megjelenik egy üzenet, amely megadja, hogy az erőforrás nincs-e konfigurálva.

1. A portálon tekintse meg a minified kivétel részleteit.
2. Kattintson a *unkisebbít*
3. Ha az erőforrás nincs konfigurálva, egy üzenet jelenik meg, amely felszólítja a konfigurálására.

### <a name="from-the-properties-page"></a>A tulajdonságok lapról

Ha szeretné konfigurálni vagy módosítani a Application Insights erőforráshoz csatolt Storage-fiókot vagy BLOB-tárolót, megtekintheti a Application Insights erőforrás *Tulajdonságok* lapját.

1. Navigáljon a Application Insights erőforrás *Tulajdonságok* lapjára.
2. Kattintson a *change Source Map blob-tároló* elemre.
3. Válasszon másik BLOB-tárolót a forrás Maps-tárolóként.
4. Kattintson a `Apply` gombra.

> [!div class="mx-imgBorder"]
> ![Konfigurálja újra a kiválasztott Azure BLOB-tárolót a Tulajdonságok panelen való navigálással](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="required-azure-role-based-access-control-azure-rbac-settings-on-your-blob-container"></a>Szükséges Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) beállításai a blob-tárolón

A portálon található bármely felhasználónak, amely ezt a funkciót használja, legalább [Storage blob-Adatolvasóként][storage blob data reader] kell hozzárendelni a blob-tárolóhoz. Ezt a szerepkört olyan másnak kell társítania, amely ezen a funkción keresztül fogja használni a forrás-leképezéseket.

> [!NOTE]
> A tároló létrehozásának módjától függően előfordulhat, hogy a rendszer nem rendeli hozzá automatikusan Önhöz vagy a csapatához.

### <a name="source-map-not-found"></a>A forrás-hozzárendelés nem található

1. Ellenőrizze, hogy a megfelelő forrás-hozzárendelés fel van-e töltve a megfelelő blob-tárolóba
2. Győződjön meg arról, hogy a forrás-hozzárendelési fájl neve a (z) nevű JavaScript-fájl után van elnevezve, utótag: `.map` .
    - Például `/static/js/main.4e2ca5fa.chunk.js` megkeresi a nevű blobot. `main.4e2ca5fa.chunk.js.map`
3. Ellenőrizze, hogy a böngésző konzolján látható-e a hibák naplózása. Adja meg ezt minden támogatási jegyben.

## <a name="next-steps"></a>Következő lépések

* [Azure file Copy feladat](/azure/devops/pipelines/tasks/deploy/azure-file-copy)


<!-- Remote URLs -->
[create storage account]: ../../storage/common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: ../../storage/blobs/storage-quickstart-blobs-portal.md
[storage blob data reader]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme
