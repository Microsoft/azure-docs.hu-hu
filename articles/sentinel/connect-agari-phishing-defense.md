---
title: A Agari phishing Defense-és Brand Protection-megoldások összekapcsolása az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan használhatja a Agari adathalászat-védelmi és Brand Protection-összekötőt a naplók Azure Sentinelbe való lekéréséhez. Megtekintheti a munkafüzetek Agari, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 3684b5a8069a6b683ae562c527af89814362a7f9
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806986"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>A Agari phishing Defense-és Brand Protection-megoldások összekapcsolása az Azure Sentinel szolgáltatással

> [!IMPORTANT]
> A Agari phishing Defense és a Brand Protection-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

A Agari phishing Defense és a Brand Protection Connector segítségével könnyedén csatlakoztathatja a Brand Protection-és az adathalászat-védelmi megoldások naplóit az Azure Sentinelhez, így megtekintheti a munkafüzetek adatait, lekérdezheti az egyéni riasztásokat, és beépítheti azt a vizsgálat javítására. A Agari megoldásai Azure Functions és REST API használatával integrálhatók az Azure Sentinel szolgáltatással.

Emellett a márka-és adathalászat-visszajelzéssel rendelkező ügyfelek a biztonsági Graph API használatával kihasználhatják a fenyegetések elleni megosztás előnyeit.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek a Agari adathalászat-védelmi és Brand Protection-megoldásainak az Azure Sentinelhez való összekapcsolásához:

- Olvasási és írási engedélyek az Azure Sentinel munkaterületen.

- Olvasási engedélyek a munkaterület megosztott kulcsaihoz. [További információ a munkaterület kulcsairól](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

- A függvényalkalmazás létrehozásához szükséges engedélyek beolvasása és írása Azure Functionson. [További információ a Azure Functionsról](../azure-functions/index.yml).

- Győződjön meg arról, hogy rendelkezik a Agari- **ügyfél-azonosítóval** és a **titkos kulcsokkal** (az összes Agari-megoldásban azonos). Útmutatásért tekintse meg a [Agari Developers webhelyet](https://developers.agari.com/agari-platform/docs/quick-start) .

## <a name="configure-and-connect-agari-solutions"></a>Agari-megoldások konfigurálása és összekapcsolása 

A Agari-megoldások Azure-függvényalkalmazás használatával integrálhatók és exportálhatók a naplók közvetlenül az Azure Sentinelbe.

> [!NOTE]
> Ez az összekötő Azure Functions használatával csatlakozik a Agari megoldásaihoz, hogy lekérje a naplókat az Azure Sentinelbe. Ennek eredményeként további adatfeldolgozási költségek is megjelenhetnek. A részletekért olvassa el a [Azure functions díjszabását](https://azure.microsoft.com/pricing/details/functions/) ismertető oldalt.

1. **Gyűjtse össze a Agari API hitelesítő adatait:** 

    Győződjön meg arról, hogy rendelkezik a Agari- **ügyfél-azonosítóval** és a **titkos kulcsokkal**. Az utasítások a [Agari Developers webhelyen](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials)találhatók.

1. **Választható A biztonsági Graph API engedélyezése:** 

    A Agari függvényalkalmazás lehetővé teszi, hogy a biztonsági Graph API használatával megossza a fenyegetésekkel kapcsolatos intelligenciát az Azure Sentinel szolgáltatással. A funkció használatához engedélyeznie kell a [Sentinel Threat Intelligence Platforms Connectort](connect-threat-intelligence.md) , és [regisztrálnia kell egy alkalmazást](/graph/auth-register-app-v2) Azure Active Directoryban.

    Ez a folyamat három információt nyújt az alábbi függvényalkalmazás telepítésekor: a **Graph bérlő azonosítója**, a **gráf ügyfél-azonosítója** és a **Graph ügyfél titka**.

1. **Telepítse az összekötőt és a hozzá tartozó Azure függvényalkalmazás:** 

    1. Az Azure Sentinel portálon válassza az **adatösszekötők** lehetőséget. Válassza az **Agari phishing Defense és a Brand Protection (előzetes verzió)** lehetőséget, majd **nyissa meg az összekötő lapot**.

    1. A **konfiguráció** területen másolja az Azure Sentinel- **munkaterület azonosítóját** és az **elsődleges kulcsot** , és illessze be őket.

    1. Válassza **az üzembe helyezés az Azure-** ban lehetőséget. (Előfordulhat, hogy a gomb megkereséséhez le kell görgetni a gombot.)

    1. Ekkor megjelenik az **Egyéni üzembe helyezés** képernyő.

        - Adja meg a Agari **ügyfél-azonosítóját** és az **ügyfél titkos** kulcsát (titkos kulcsok)

        - Adja meg az Azure Sentinel- **munkaterület azonosítóját** és a vágólapra másolt, illetve az azt felhelyezett **munkaterület-kulcsot** (elsődleges kulcs).

        - Válassza az **igaz** vagy a **hamis** lehetőséget azon Agari-megoldások esetében, amelyekhez aktív előfizetések tartoznak.

        - Ha létrehozott egy Azure-alkalmazást a IoCs az Azure Sentinel használatával való megosztásához a biztonsági Graph API segítségével, válassza az **igaz** lehetőséget a **biztonsági gráf megosztásának engedélyezéséhez** , majd adja meg a **Graph bérlői azonosítót**, a **Graph ügyfél-azonosítót** és a **Graph-ügyfél titkát**.

    1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az ellenőrzés befejeződése után kattintson a **Létrehozás** gombra.

1. **Rendelje hozzá a szükséges engedélyeket a függvényalkalmazáshoz:**

    Az Agari-összekötő környezeti változót használ a naplózási hozzáférési időbélyegek tárolásához. Ahhoz, hogy az alkalmazás írható legyen ehhez a változóhoz, az engedélyeket hozzá kell rendelni a rendszerhez rendelt identitáshoz.

    1. A Azure Portal navigáljon a **függvényalkalmazás**.

    1. A **függvényalkalmazás** panelen válassza ki a függvényalkalmazás a listából, majd válassza az **identitás** elemet a Függvényalkalmazás navigációs menüjének **Beállítások** területén.

    1. A **rendszerhez rendelt** lapon állítsa be az **állapotot** **a** következőre:. 

    1. Kattintson a **Save (Mentés**) gombra, és megjelenik egy Azure- **szerepkör hozzárendelése** gomb. Kattintson rá.

    1. Az **Azure szerepkör-hozzárendelések** képernyőn válassza a **szerepkör-hozzárendelés hozzáadása** elemet. Állítsa be az **előfizetéshez** a **hatókört** , válassza ki az előfizetést az **előfizetés** legördülő menüből, és állítsa be a **szerepkört** az **alkalmazás konfigurációs adatainak tulajdonosának**. 

    1. Kattintson a **Mentés** gombra.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a **naplók** területen, a *CustomLogs* alatt, az alábbi táblázatokban jelenik meg: 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

A Agari-megoldások lekérdezéséhez a lekérdezési ablakban adja meg a fenti táblanév egyikét.

Néhány hasznos minta lekérdezéshez tekintse meg az összekötő lap **következő lépések** lapját.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan kapcsolódhat a Agari phishing Defense-és Brand Protection-megoldásokhoz az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
