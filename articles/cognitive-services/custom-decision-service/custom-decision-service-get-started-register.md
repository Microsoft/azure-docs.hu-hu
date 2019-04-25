---
title: A Custom Decision Service - alkalmazás regisztrálása
titlesuffix: Azure Cognitive Services
description: Egy lépésenkénti útmutató az új alkalmazás regisztrálása az Azure Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo
ms.openlocfilehash: c56aef180f5e16d2ec1931caabe04295ef288ca9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60509771"
---
# <a name="register-your-application"></a>Alkalmazás regisztrálása

A Custom Decision Service az alkalmazás használatához regisztrálni a portálon. Ez a cikk azt ismerteti, hogyan.

1. Nyissa meg a [első oldal](https://portal.ds.microsoft.com/) a Custom Decision Service. Kattintson a menüszalag **saját portál**, az ábrán a kiemelt:

    ![A portálon](./media/portal.png)

    Ha Ön nem már bejelentkezett, a a portál felszólítja, hogy jelentkezzen be a [Microsoft-fiók](https://account.microsoft.com/account). Miután bejelentkezett, a portál megjeleníti a Microsoft-fiók az oldal jobb felső sarkában.

2. Az alkalmazás regisztrálásához kattintson a **új alkalmazás** gombra.

3. A párbeszédpanelen válassza ki az alkalmazás Alkalmazásazonosítója. A Custom Decision Service minden alkalmazáshoz egyedi azonosító szükséges. Ha valaki más már van foglalt ezt az Azonosítót, a rendszer megkérdezi, hogy válasszon egy másikat.

4. Adjon meg egy művelet API-t. Ez a beállítás egy RSS vagy Atom-hírcsatorna, amely az elérhető tartalmak, hogy az alkalmazás a Custom Decision Service kommunikál. Adja meg a csatorna nevét, és adja meg az URL-címet, amelyről szolgálja. Ehhez később ezzel a lépéssel, kattintson a **hírcsatornák** gombra, majd a **új adatcsatorna** gombra. Egy példa, amely létrehoz egy RSS-hírcsatorna a későbbiekben olvashat.

5. Az alkalmazás regisztrálásához, válassza ki a **egyéni alkalmazás** jelölőnégyzetet a bal alsó sarokban. Adjon meg egy [kapcsolati karakterlánc](../../storage/common/storage-configure-connection-string.md) számára az Azure storage-fiókot, ahol a rendszer az alkalmazás adatokat naplózza. Tárfiók létrehozásával kapcsolatos további információkért lásd: [létrehozása, kezelése vagy törölheti a tárfiókokat](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>További lépések

* Első lépések a [weblap](custom-decision-service-get-started-browser.md) vagy [okostelefon alkalmazás](custom-decision-service-get-started-app.md) optimalizálásához.
* Haladjon végig a [oktatóanyag](custom-decision-service-tutorial-news.md) részletesebb példát.
* Az [API-referencia](custom-decision-service-api-reference.md) még további információkat tartalmaz a nyújtott funkciókról.
