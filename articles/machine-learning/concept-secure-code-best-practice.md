---
title: Biztonságos kód – ajánlott eljárások
titleSuffix: Azure Machine Learning
description: Ismerje meg azokat a lehetséges biztonsági fenyegetéseket, amelyek a Azure Machine Learning, a mérséklések és az ajánlott eljárások fejlesztésekor előfordulhatnak.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: 37cb70bdbd1e3c87eeb994e0959c6214822d22ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93322971"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>Ajánlott eljárások a szabályzatok biztonságossá tételéhez Azure Machine Learning

Azure Machine Learning a fájlokat és tartalmakat bármilyen forrásból feltöltheti az Azure-ba. A Jupyter-jegyzetfüzetek vagy a betöltött parancsfájlok tartalma beolvashatja a munkamenetek adatait, hozzáférhet a szervezeten belüli adatokhoz az Azure-ban, vagy kártékony folyamatokat futtathat az Ön nevében.

> [!IMPORTANT]
> Csak megbízható forrásból származó jegyzetfüzeteket vagy parancsfájlokat futtasson. Például, ha Ön vagy a biztonsági csapata áttekintette a jegyzetfüzetet vagy a parancsfájlt.

## <a name="potential-threats"></a>Lehetséges fenyegetések

A Azure Machine Learning-fejlesztés gyakran webes fejlesztési környezeteket (notebookok & Azure ML Studio) is magában foglal. Webalapú fejlesztői környezetek használatakor a lehetséges fenyegetések a következők:

* [Helyek közötti parancsfájlok (XSS)](https://owasp.org/www-community/attacks/xss/)

    * __Dom-injektálás__: Ez a típusú támadás módosíthatja a böngészőben megjelenő felhasználói felületet. Például úgy, hogy a Futtatás gomb egy Jupyter Notebookban való működésének módosítását végzi.
    * __Hozzáférési jogkivonat/cookie-__ k: az XSS-támadások a helyi tárolók és a böngésző cookie-jai is hozzáférhetnek. A Azure Active Directory (HRE) hitelesítési token a helyi tárolóban van tárolva. Egy XSS-támadás ezt a tokent használva API-hívásokat hajthat végre az Ön nevében, majd elküldheti az adatait egy külső rendszernek vagy API-nak.

* [Helyek közötti kérelem hamisítása (CSRF)](https://owasp.org/www-community/attacks/csrf): Ez a támadás lecserélheti egy rendszerkép URL-címét, vagy egy rosszindulatú parancsfájl vagy API URL-címével. Ha a rendszerkép betöltődik, vagy hivatkozásra kattint, a rendszer meghívja az URL-címet.

## <a name="azure-ml-studio-notebooks"></a>Azure ML Studio-jegyzetfüzetek

A Azure Machine Learning Studio üzemeltetett notebook-élményt biztosít a böngészőben. A jegyzetfüzetben található cellák a kártékony kódokat tartalmazó HTML-dokumentumokat vagy-töredékeket is tartalmazhatnak.  A kimenet megjelenítésekor a kód végrehajtható.

__Lehetséges fenyegetések__:
* Helyek közötti parancsfájlok (XSS)
* Helyek közötti kérelmek hamisítása (CSRF)

__A Azure Machine learning által biztosított enyhítések__:
* A __kód cellájának kimenete__ iframe-ben van lefoglalva. Az IFRAME megakadályozza, hogy a parancsfájl hozzáférjen a szülő DOM, a cookie-khoz vagy a munkamenet-tárolóhoz.
* A __Markdown__ a dompurify könyvtár használatával tisztítja. Ez blokkolja a rosszindulatú parancsfájlok futtatását a Markdown-cellákkal.
* A rendszer a __KÉPurl-címeket__ és a __Markdown-hivatkozásokat__ egy Microsoft tulajdonú végpontnak küldi el, amely rosszindulatú értékeket keres. Ha a rendszer kártékony értéket észlel, a végpont elutasítja a kérelmet.

__Ajánlott műveletek__:
* Győződjön meg arról, hogy a studióba való feltöltés előtt megbízik a fájlok tartalmában. A feltöltéskor meg kell erősítenie, hogy megbízható fájlokat tölt fel.
* Egy külső alkalmazás megnyitására szolgáló hivatkozás kiválasztásakor a rendszer felszólítja, hogy Bízzon meg az alkalmazásban.

## <a name="azure-ml-compute-instance"></a>Azure ML számítási példány

Azure Machine Learning számítási példány __Jupyter__ és __Jupyter labort__ üzemeltet. Ha a vagy a használatával, a jegyzetfüzetben vagy a kódban található cellákban a HTML-dokumentumok vagy a kártékony kódokat tartalmazó töredékek is megadhatók. A kimenet megjelenítésekor a kód végrehajtható. Ugyanezek a fenyegetések is érvényesek, ha számítási példányon üzemeltetett __RStudio__ használ.

__Lehetséges fenyegetések__:
* Helyek közötti parancsfájlok (XSS)
* Helyek közötti kérelmek hamisítása (CSRF)

__A Azure Machine learning által biztosított enyhítések__:
* Nincsenek. A Jupyter és a Jupyter Lab a Azure Machine Learning számítási példányon üzemeltetett nyílt forráskódú alkalmazások.

__Ajánlott műveletek__:
* Győződjön meg arról, hogy a studióba való feltöltés előtt megbízik a fájlok tartalmában. A feltöltéskor meg kell erősítenie, hogy megbízható fájlokat tölt fel.

## <a name="report-security-issues-or-concerns"></a>Biztonsági problémák és problémák jelentése 

Azure Machine Learning jogosult a Microsoft Azure Bounty program keretében. További információért látogasson el a következő oldalra:  [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure) .

## <a name="next-steps"></a>Következő lépések

* [Vállalati biztonsági Azure Machine Learning](concept-enterprise-security.md)