---
title: Tudásbázis kialakítása – QnA Maker fogalmak
description: Megtudhatja, hogyan tervezhet tudásbázist – QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c21c82b5fd024598da30f4ac7b1ed01e64561e3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000113"
---
# <a name="question-and-answer-pair-concepts"></a>Kérdések és válaszok – pár fogalmak

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

![QnA Maker tudásbázisok](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Szerkesztői Hozzáadás a tudásbázishoz

Ha nem rendelkezik már meglévő tartalommal a Tudásbázis feltöltéséhez, QnA-párokat adhat hozzá a QnA Maker portálon. Megtudhatja, hogyan frissítheti a tudásbázist [itt](../How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>A Tudásbázis helyi szerkesztése

A Tudásbázis létrehozása után javasolt a [QnA Maker-portálon](https://qnamaker.ai)lévő Tudásbázis szövegének szerkesztése a helyi fájlok exportálása és újraimportálása helyett. Előfordulhat azonban, hogy a tudásbázist helyileg kell szerkesztenie.

Exportálja a tudásbázist a **Beállítások** lapról, majd szerkessze a tudásbázist a Microsoft Excelben. Ha egy másik alkalmazás használatával szerkeszti az exportált fájlt, előfordulhat, hogy az alkalmazás szintaktikai hibákat tartalmaz, mert az nem teljes TSV-kompatibilis. A Microsoft Excel TSV-fájljai általában nem vezetnek be formázási hibákat.

Miután végzett a szerkesztéssel, importálja újra a TSV-fájlt a **Beállítások** lapról. Ez teljes mértékben lecseréli az aktuális tudásbázist az importált tudásbázisba.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudásbázis életciklusa QnA Maker](./development-lifecycle-knowledge-base.md)