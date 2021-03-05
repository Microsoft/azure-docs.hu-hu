---
title: Szolgáltatás konfigurációja – QnA Maker
description: Az erőforrások konfigurálásának ismertetése.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: da46084c8c2616284c31ef155927e8dbcbd19e0e
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209359"
---
# <a name="service-configuration"></a>Szolgáltatáskonfiguráció

QnA Maker minden verziója az Azure-erőforrások (szolgáltatások) egy másik készletét használja. Ez a cikk a szolgáltatások támogatott testreszabásait ismerteti. 

## <a name="app-service"></a>App Service

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

A QnA Maker a App Service használatával biztosítja a [GENERATEANSWER API](/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer)által használt lekérdezési futtatókörnyezetet.

Ezek a beállítások a App Service Azure Portal érhetők el. A beállítások a **Beállítások**, majd a **konfiguráció** lehetőség kiválasztásával érhetők el.

Az egyes beállításokat az Alkalmazásbeállítások listáján keresztül is megadhatja, vagy a **Speciális szerkesztés** lehetőség kiválasztásával több beállítást is módosíthat.

|Erőforrás|Beállítás|
|--|--|
|AzureSearchAdminKey|Cognitive Search – QnA pár Storage és ranker #1|
|AzureSearchName|Cognitive Search – QnA pár Storage és ranker #1|
|DefaultAnswer|A válasz szövege, ha nem található egyezés|
|UserAppInsightsAppId|Csevegési napló és telemetria|
|UserAppInsightsKey|Csevegési napló és telemetria|
|UserAppInsightsName|Csevegési napló és telemetria|
|QNAMAKER_EXTENSION_VERSION|Mindig a _legújabb_ értékre van állítva. Ez a beállítás inicializálja a QnAMaker-hely bővítményt a App Serviceban.|

A módosítások végrehajtása után **újra kell indítania** a szolgáltatást a Azure Portal **Áttekintés** lapjáról.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

App Service testreszabások nem vonatkoznak a QnA Maker felügyelt (előzetes verzió) elemre.

---

## <a name="qna-maker-service"></a>QnA Maker szolgáltatás

A QnA Maker szolgáltatás olyan konfigurációt biztosít az alábbi felhasználók számára, akik együttműködnek egyetlen QnA Maker szolgáltatásban és annak összes tudásbázisában.

Ismerje meg [, hogyan adhat közreműködőket](./reference-role-based-access-control.md) a szolgáltatáshoz.

## <a name="change-azure-cognitive-search"></a>Azure-Cognitive Search módosítása

Megtudhatja [, hogyan módosíthatja a](./how-to/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA Maker szolgáltatáshoz kapcsolódó Cognitive Search szolgáltatást.

## <a name="change-default-answer"></a>Alapértelmezett válasz módosítása

Megtudhatja [, hogyan módosíthatja az alapértelmezett válaszok szövegét](How-To/change-default-answer.md). 

## <a name="telemetry"></a>Telemetria

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

A Application Insights a QnA Maker GA telemetria figyelésére használható. Nincsenek QnA Makerra vonatkozó konfigurációs beállítások.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

Ismerje meg [, hogyan adhat hozzá telemetria a QnA Maker felügyelt (előzetes verzió) szolgáltatáshoz](How-To/get-analytics-knowledge-base.md). 

---

## <a name="app-service-plan"></a>App Service Plan (App Service-csomag)

# <a name="qnamaker-ga-stable-release"></a>[QnAMaker GA (stabil kiadás)](#tab/v1)

App Service a csomag nem rendelkezik a QnA Makerra vonatkozó konfigurációs beállításokkal.

# <a name="qnamaker-managed-preview-release"></a>[QnAMaker által felügyelt (előzetes verzió)](#tab/v2)

A App Service-csomag nem használatos QnA Maker felügyelt (előzetes verzió).

---

## <a name="next-steps"></a>Következő lépések

További információ a tudásbázisba importálni kívánt dokumentumok és URL-címek [formátumáról](reference-document-format-guidelines.md) .
