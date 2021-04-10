---
title: A log Analytics-nézetek telepítése és használata | Microsoft Docs
description: Ismerje meg, hogyan telepítheti és használhatja a log Analytics-nézeteket Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86ad698793d562f93f9972903ca21e50c209c79c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580001"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>A log Analytics-nézetek telepítése és használata Azure Active Directory

A Azure Active Directory log Analytics-nézetek segítségével elemezheti és keresheti meg az Azure ad-tevékenység naplóit az Azure AD-bérlőben. Az Azure AD-tevékenységek naplói a következők:

* Naplók: a [naplózási tevékenység jelentés](concept-audit-logs.md) hozzáférést biztosít a bérlőn végrehajtott összes feladat előzményeihez.
* Bejelentkezési naplók: a [bejelentkezési tevékenység jelentésével](concept-sign-ins.md)meghatározhatja, hogy ki hajtotta végre a naplókban jelentett feladatokat.

## <a name="prerequisites"></a>Előfeltételek

A log Analytics-nézetek használatához a következőkre lesz szüksége:

* Egy Log Analytics munkaterület az Azure-előfizetésében. Megtudhatja, hogyan [hozhat létre log Analytics munkaterületet](../../azure-monitor/logs/quick-create-workspace.md).
* Először végezze el az [Azure ad-tevékenység naplófájljainak a log Analytics munkaterületre történő átirányításához](howto-integrate-activity-logs-with-log-analytics.md)szükséges lépéseket.
* Töltse le a nézeteket a [GitHub-tárházból](https://aka.ms/AADLogAnalyticsviews) a helyi számítógépre.

## <a name="install-the-log-analytics-views"></a>A log Analytics-nézetek telepítése

1. Navigáljon a Log Analytics munkaterületre. Ehhez először navigáljon a [Azure Portal](https://portal.azure.com) , és válassza a **minden szolgáltatás** lehetőséget. Írja be a **log Analytics** szöveget a szövegmezőbe, és válassza a **log Analytics munkaterületek** lehetőséget. Válassza ki azt a munkaterületet, amelybe a tevékenység naplóit átirányította, az előfeltételek részeként.
2. Válassza a **Tervező nézet** lehetőséget, válassza az **Importálás** lehetőséget, majd válassza a **fájl kiválasztása** lehetőséget a nézetek importálásához a helyi számítógépről.
3. Válassza ki az előfeltételekből letöltött nézeteket, majd válassza a **Mentés** lehetőséget az importálás mentéséhez. Ezt az **Azure ad-fiók kiépítési eseményeinek** és a **bejelentkezési események** nézetének megváltoztatásával teheti meg.

## <a name="use-the-views"></a>A nézetek használata

1. Navigáljon a Log Analytics munkaterületre. Ehhez először navigáljon a [Azure Portal](https://portal.azure.com) , és válassza a **minden szolgáltatás** lehetőséget. Írja be a **log Analytics** szöveget a szövegmezőbe, és válassza a **log Analytics munkaterületek** lehetőséget. Válassza ki azt a munkaterületet, amelybe a tevékenység naplóit átirányította, az előfeltételek részeként.

2. Miután megtörtént a munkaterületen, válassza a **munkaterület összegzése** elemet. A következő három nézetnek kell megjelennie:

    * **Azure ad-fiók kiépítési eseményei**: ebben a nézetben a naplózási kiépítési tevékenységgel kapcsolatos jelentések láthatók, például az új felhasználók kiépített és kiépítési hibák száma, a frissített és a frissítési hibák száma, valamint a kiosztott és a hozzájuk kapcsolódó hibák száma.    
    * **Bejelentkezési események**: ebben a nézetben láthatók a figyelési tevékenységekkel kapcsolatos legfontosabb jelentések, például az alkalmazások, a felhasználók, az eszközök, valamint az összegző nézetek, amelyek az idő múlásával követik nyomon a bejelentkezések számát.

3. Válassza ki az egyes nézetek bármelyikét, hogy beugorjon az egyes jelentésekre. Riasztásokat is beállíthat a jelentési paraméterek bármelyikén. Tegyük fel például, hogy riasztást állítunk be minden alkalommal, amikor bejelentkezési hiba történt. Ehhez először jelölje ki a **bejelentkezési események** nézetet, válassza a **bejelentkezési hibák időbeli** jelentéssel lehetőséget, majd válassza az **elemzés** lehetőséget a Részletek lap megnyitásához a jelentés mögötti tényleges lekérdezéssel. 

    ![A képernyőképen a jelentés lekérdezését tartalmazó Analytics-Részletek lap látható.](./media/howto-install-use-log-analytics-views/details.png)


4. Válassza a **riasztás beállítása** lehetőséget, majd válassza ki, hogy az egyéni naplók keresésekor a **riasztási feltételek** szakaszban ne **legyen &lt; meghatározva &gt; a logikai** érték. Mivel a bejelentkezési hiba miatt riasztást szeretnénk kapni, állítsa az alapértelmezett riasztási logika **küszöbértékét** **1-re** , majd válassza a **kész** lehetőséget. 

    ![Jellogika konfigurálása](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Adja meg a riasztás nevét és leírását, és állítsa a súlyosságot **figyelmeztetésre**.

    ![Szabály létrehozása](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Válassza ki a riasztásra felvenni kívánt műveleti csoportot. Általánosságban elmondható, hogy ez lehet egy csapat, amelyet e-mailben vagy SMS-ben szeretne értesíteni, vagy lehet egy automatizált feladat webhookok, runbookok, functions, Logic apps vagy külső ITSM-megoldások használatával. Megtudhatja, hogyan [hozhat létre és kezelhet műveleti csoportokat a Azure Portalban](../../azure-monitor/alerts/action-groups.md).

7. A riasztás létrehozásához válassza a **riasztási szabály létrehozása** lehetőséget. Ekkor a rendszer minden alkalommal riasztást küld, amikor bejelentkezési hiba történt.

## <a name="next-steps"></a>Következő lépések

* [A Tevékenységnaplók elemzése Azure Monitor naplókkal](howto-analyze-activity-logs-log-analytics.md)
* [Ismerkedés a Azure Monitor-naplók Azure Portal](../../azure-monitor/logs/log-analytics-tutorial.md)