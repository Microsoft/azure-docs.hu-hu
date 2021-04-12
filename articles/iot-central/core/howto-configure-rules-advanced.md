---
title: Munkafolyamatok használata az Azure IoT Central alkalmazás integrálásához más Cloud Services-szolgáltatásokkal | Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálhatja a IoT Central alkalmazást más felhőalapú szolgáltatásokkal integráló szabályokat és műveleteket. Speciális szabály létrehozásához IoT Central-összekötőt kell használnia a Power automatizáló vagy a Azure Logic Appsban.
author: dominicbetts
ms.author: dobett
ms.date: 05/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: af6adcf408c9790b09830e986cd1cac00774a2ec
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491933"
---
# <a name="use-workflows-to-integrate-your-azure-iot-central-application-with-other-cloud-services"></a>Munkafolyamatok használata az Azure IoT Central alkalmazás integrálásához más felhőalapú szolgáltatásokkal

*Ez a cikk a megoldás-építők esetében érvényes.*

Olyan IoT Central szabályokat hozhat létre, amelyek olyan műveleteket aktiválnak, mint például az e-mailek küldése a telemetria feltételekre adott válaszként, például az eszköz hőmérséklete meghaladja a küszöbértéket.

A Power automatizálás és a Azure Logic Apps Azure IoT Central v3-összekötője lehetővé teszi, hogy fejlettebb szabályokat hozzon létre a IoT Central-műveletek automatizálásához:

- Ha egy szabály az Azure IoT Central-alkalmazásban következik be, akkor kiválthat egy munkafolyamatot a Power automatizálásban vagy Azure Logic Apps. Ezek a munkafolyamatok más felhőalapú szolgáltatásokban, például Microsoft 365 vagy külső szolgáltatásokban is futtathatnak műveleteket.
- Egy másik felhőalapú szolgáltatásban (például Microsoft 365) lévő esemény aktiválhat egy munkafolyamatot az automatizálásban vagy Azure Logic Apps. Ezek a munkafolyamatok futtathatják a műveleteket, vagy lekérhetik az adatok beolvasását a IoT Central alkalmazásból.

## <a name="prerequisites"></a>Előfeltételek

A jelen útmutató lépéseinek végrehajtásához aktív Azure-előfizetésre van szükség. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

A megoldás beállításához a 3. verziójú IoT Central alkalmazásra van szükség. Az alkalmazás verziószámának megismeréséhez tekintse meg [az alkalmazásról szóló](./howto-get-app-info.md)témakört. IoT Central alkalmazások létrehozásával kapcsolatos további információkért lásd: [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md).

> [!NOTE]
> Ha 2. verziójú IoT Central alkalmazást használ, tekintse meg a következő témakört: [munkafolyamatok létrehozása az IoT Central-összekötővel Azure Logic apps](/previous-versions/azure/iot-central/core/howto-build-azure-logic-apps) az előző verziók dokumentációs webhelyén, és az Azure IoT Central v2-összekötő használata

## <a name="trigger-a-workflow-from-a-rule"></a>Munkafolyamat elindítása szabályból

Mielőtt elindít egy munkafolyamatot az automatizálásban vagy Azure Logic Appsban, szüksége lesz egy szabályra a IoT Central alkalmazásban. További információ: [szabályok és műveletek konfigurálása az Azure IoT Centralban](./howto-configure-rules.md).

Az **Azure IoT Central v3 – előzetes** verziójú összekötő hozzáadása triggerként a Power automatizálásban:

1. A Power automatizáló lapon válassza a **+ Létrehozás** lehetőséget, majd válassza az **Egyéni** lapot.
1. Keresse meg *IoT Central*, majd válassza ki az **Azure IoT Central v3-Preview** összekötőt.
1. Az eseményindítók listájában válassza ki, hogy **Mikor indul el egy szabály (előzetes verzió)**.
1. A **szabály indításakor** lépésnél válassza ki a IoT Central alkalmazást és a használni kívánt szabályt.

Az **Azure IoT Central v3 – előzetes** verziójú összekötő hozzáadása triggerként a Azure Logic Appsban:

1. **Logic apps Designerben** válassza ki az **üres logikai alkalmazás** sablonját.
1. A tervezőben válassza az **Egyéni** lapot.
1. Keresse meg *IoT Central*, majd válassza ki az **Azure IoT Central v3-Preview** összekötőt.
1. Az eseményindítók listájában válassza ki, hogy **Mikor indul el egy szabály (előzetes verzió)**.
1. A **szabály indításakor** lépésnél válassza ki a IoT Central alkalmazást és a használni kívánt szabályt.

:::image type="content" source="./media/howto-configure-rules-advanced/triggers.png" alt-text="Keresse meg az Azure IoT Central-Preview összekötőt, és válassza ki a triggert":::

Mostantól további lépéseket is hozzáadhat a munkafolyamathoz az integrációs forgatókönyv kiépítéséhez.

## <a name="run-an-action"></a>Művelet futtatása

A IoT Central alkalmazásban műveleteket futtathat a Power automatizáló és a Azure Logic Apps munkafolyamatok használatával. Először hozza létre a munkafolyamatot, és használjon egy összekötőt a munkafolyamat elindításához. Ezután használja az **Azure IoT Central v3 – Preview** összekötőt műveletként.

Az **Azure IoT Central v3 – előzetes** verziójú összekötő hozzáadása műveletként a Power automatizálásban:

1. A energiagazdálkodás automatizálása területen a **művelet kiválasztása** panelen válassza az **Egyéni** lapot.
1. Keresse meg *IoT Central* , és válassza ki az **Azure IoT Central v3-Preview** összekötőt.
1. A műveletek listájában válassza ki a használni kívánt IoT Central műveletet.
1. A művelet lépésben fejezze be a kiválasztott művelet konfigurációját. Kattintson a **Mentés** gombra.

Az **Azure IoT Central v3 – Preview** összekötő hozzáadása műveletként a Azure Logic Appsban:

1. **Logic apps Designerben** a **művelet kiválasztása** panelen válassza az **Egyéni** lapot.
1. Keresse meg *IoT Central*, majd válassza ki az **Azure IoT Central v3-Preview** összekötőt.
1. A műveletek listájában válassza ki a használni kívánt IoT Central műveletet.
1. A művelet lépésben fejezze be a kiválasztott művelet konfigurációját. Kattintson a **Mentés** gombra.

:::image type="content" source="./media/howto-configure-rules-advanced/actions.png" alt-text="Keresse meg az Azure IoT Central v3-összekötőt, és válasszon egy műveletet":::

## <a name="list-of-actions"></a>Műveletek listája

A következő lista az **Azure IoT Central v3 – előzetes** verziójú összekötőben elérhető IoT Central műveleteket és azok konfigurációs beállításait mutatja be. Számos mezőhöz dinamikusan generált tartalom tartozhat. Egy korábbi lépés például meghatározhatja az eszköz AZONOSÍTÓját, amelyre az aktuális lépés érvényes.

### <a name="create-or-update-a-device"></a>Eszköz létrehozása vagy frissítése

Ezzel a művelettel hozhat létre vagy frissíthet egy eszközt a IoT Central alkalmazásban.

| Mező | Leírás |
| ----- | ----------- |
| Alkalmazás | Válasszon a IoT Central alkalmazások listájából. |
| Eszköz | A létrehozandó vagy frissítendő eszköz egyedi azonosítója. |
| Approved | Válassza ki, hogy az eszközt jóváhagyták-e a IoT Centralhoz való kapcsolódáshoz. |
| Eszköz leírása | Az eszköz részletes leírása. |
| Eszköz neve | Az eszköz megjelenített neve. |
| Eszköz sablonja | Válasszon az IoT Central alkalmazásban található eszközök sablonjai közül. |
| Szimulált | Válassza ki, hogy az eszköz szimulálva van-e. |

### <a name="delete-a-device"></a>Eszköz törlése

Ezzel a művelettel törölhet egy eszközt a IoT Central alkalmazásból.

| Mező | Leírás |
| ----- | ----------- |
| Alkalmazás | Válasszon a IoT Central alkalmazások listájából. |
| Eszköz | A törlendő eszköz egyedi azonosítója. |

### <a name="execute-a-device-command"></a>Eszköz parancs végrehajtása

Ezzel a művelettel az eszköz egyik felületén definiált parancsot hajthat végre.

| Mező | Leírás |
| ----- | ----------- |
| Alkalmazás | Válasszon a IoT Central alkalmazások listájából. |
| Eszköz | A törlendő eszköz egyedi azonosítója. |
| Eszköz összetevő | A parancsot tartalmazó eszköz sablonja. |
| Eszközparancs | Válassza ki a kiválasztott felület egyik parancsát. |
| Eszköz sablonja | Válasszon az IoT Central alkalmazásban található eszközök sablonjai közül. |
| Eszköz parancs-kérelem hasznos adatai | Ha a parancshoz kérelem hasznos adat szükséges, adja hozzá itt.  |

> [!NOTE]
> Nem választhat eszköz-összetevőt, amíg ki nem választotta az eszköz sablonját.

### <a name="get-a-device-by-id"></a>Eszköz lekérése azonosító alapján

Ezzel a művelettel kérheti le az eszköz részleteit.

| Mező | Leírás |
| ----- | ----------- |
| Alkalmazás | Válasszon a IoT Central alkalmazások listájából. |
| Eszköz | A törlendő eszköz egyedi azonosítója. |

A visszaadott adatokat a dinamikus kifejezésekben is használhatja más műveletekben. A visszaadott eszköz adatai a következők: **jóváhagyva**, **szövegtörzs**, **eszköz leírása**, **eszköznév**, **eszköz sablonja**, **kiépített** és **szimulált**.

### <a name="get-device-cloud-properties"></a>Eszköz Felhőbeli tulajdonságainak beolvasása

Ezzel a művelettel kérheti le egy adott eszközhöz tartozó Cloud Property értékeit.

| Mező | Leírás |
| ----- | ----------- |
| Alkalmazás | Válasszon a IoT Central alkalmazások listájából. |
| Eszköz | A törlendő eszköz egyedi azonosítója. |
| Eszköz sablonja | Válasszon az IoT Central alkalmazásban található eszközök sablonjai közül. |

A visszaadott felhő tulajdonság értékeit a dinamikus kifejezésekben használhatja más műveletekben.

### <a name="get-device-properties"></a>Eszköz tulajdonságainak beolvasása

Ezzel a művelettel egy adott eszközhöz tartozó tulajdonságértékek olvashatók be.

| Mező | Leírás |
| ----- | ----------- |
| Alkalmazás | Válasszon a IoT Central alkalmazások listájából. |
| Eszköz | A törlendő eszköz egyedi azonosítója. |
| Eszköz sablonja | Válasszon az IoT Central alkalmazásban található eszközök sablonjai közül. |

A visszaadott tulajdonságértékek a dinamikus kifejezésekben is használhatók más műveletekben.

### <a name="get-device-telemetry-value"></a>Eszköz telemetria értékének beolvasása

Ezzel a művelettel kérheti le egy adott eszköz telemetria-értékeit.

| Mező | Leírás |
| ----- | ----------- |
| Alkalmazás | Válasszon a IoT Central alkalmazások listájából. |
| Eszköz | A törlendő eszköz egyedi azonosítója. |
| Eszköz sablonja | Válasszon az IoT Central alkalmazásban található eszközök sablonjai közül. |

Használhatja a visszaadott telemetria értékeket a dinamikus kifejezésekben más műveletekben.

### <a name="update-device-cloud-properties"></a>Eszköz felhő tulajdonságainak frissítése

Ezzel a művelettel frissítheti egy adott eszköz Cloud Property értékeit.

| Mező | Leírás |
| ----- | ----------- |
| Alkalmazás | Válasszon a IoT Central alkalmazások listájából. |
| Eszköz | A törlendő eszköz egyedi azonosítója. |
| Eszköz sablonja | Válasszon az IoT Central alkalmazásban található eszközök sablonjai közül. |
| Felhőtulajdonságok | Miután kiválasztotta az eszköz sablonját, a rendszer hozzáad egy mezőt a sablonban definiált minden egyes felhőalapú tulajdonsághoz. |

### <a name="update-device-properties"></a>Eszköz tulajdonságainak frissítése

Ezzel a művelettel frissítheti egy adott eszköz írható tulajdonságainak értékeit.

| Mező | Leírás |
| ----- | ----------- |
| Alkalmazás | Válasszon a IoT Central alkalmazások listájából. |
| Eszköz | A törlendő eszköz egyedi azonosítója. |
| Eszköz sablonja | Válasszon az IoT Central alkalmazásban található eszközök sablonjai közül. |
| Írható tulajdonságok | Miután kiválasztotta az eszköz sablonját, a rendszer hozzáad egy mezőt a sablonban definiált minden egyes írható tulajdonsághoz. |

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan hozhat létre egy speciális szabályt az Azure IoT Central alkalmazásban, megtudhatja, hogyan [elemezheti az eszköz adatait az azure IoT Central alkalmazásban](howto-create-analytics.md)