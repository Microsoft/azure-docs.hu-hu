---
title: Tudásbázis tervezése – QnA Maker
description: A QnA Maker Tudásbázis a kérdés-válasz típusú (QnA) és az egyes QnA-párokhoz tartozó opcionális metaadatokból áll.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: ef5476ade205109f5dfede1b3bb2c3a4ae2e81a7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94506005"
---
# <a name="question-and-answer-pair"></a>Kérdés-válasz pár

A Tudásbázis kérdések és válaszok (QnA) párokból áll.  Minden pár egyetlen választ tartalmaz, és egy pár tartalmazza az adott _válaszhoz_ tartozó összes információt. A válasz egy adatbázis-sorra vagy egy adatstruktúra-példányra is lazán hasonlíthat.

## <a name="question-and-answer-pairs"></a>Kérdés-válasz párok

A kérdés-válasz (QnA) pár **kötelező** beállításai a következők:

* a **felhasználói lekérdezésnek a QnA Maker** gépi tanuláshoz használt szövege, amely a felhasználó kérdéséhez igazodik a különböző szóhasználattal, de ugyanaz a válasz
* a **Válasz** – a pár válasza az a válasz, amelyet a rendszer akkor ad vissza, amikor egy felhasználói lekérdezés megfelel a kapcsolódó kérdésnek

Az egyes párokat **azonosítók** jelölik.

A párok **opcionális** beállításai a következők:

* **A kérdés alternatív formái** – ez segít QnA Maker a megfelelő választ visszaadni a kérdéses szövegezések széles körére
* **Metaadatok**: a metaadatok egy QnA-párral vannak társítva, és kulcs-érték párokként jelennek meg. A metaadatok címkéi a QnA párok szűrésére és a lekérdezési egyeztetést végző készlet korlátozására használhatók.
* Többfordulatú **kérések**, amelyek a több fordulatos beszélgetés folytatására szolgálnak

![QnA Maker tudásbázisok](media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Szerkesztői Hozzáadás a tudásbázishoz

Ha nem rendelkezik már meglévő tartalommal a Tudásbázis feltöltéséhez, QnA-párokat adhat hozzá a QnA Maker portálon. Megtudhatja, hogyan frissítheti a tudásbázist [itt](How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>A Tudásbázis helyi szerkesztése

A Tudásbázis létrehozása után javasolt a [QnA Maker-portálon](https://qnamaker.ai)lévő Tudásbázis szövegének szerkesztése a helyi fájlok exportálása és újraimportálása helyett. Előfordulhat azonban, hogy a tudásbázist helyileg kell szerkesztenie.

Exportálja a tudásbázist a **Beállítások** lapról, majd szerkessze a tudásbázist a Microsoft Excelben. Ha egy másik alkalmazás használatával szerkeszti az exportált fájlt, előfordulhat, hogy az alkalmazás szintaktikai hibákat tartalmaz, mert az nem teljes TSV-kompatibilis. A Microsoft Excel TSV-fájljai általában nem vezetnek be formázási hibákat.

Miután végzett a szerkesztéssel, importálja újra a TSV-fájlt a **Beállítások** lapról. Ez teljes mértékben lecseréli az aktuális tudásbázist az importált tudásbázisba.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudásbázis életciklusa QnA Maker](Concepts/development-lifecycle-knowledge-base.md)
