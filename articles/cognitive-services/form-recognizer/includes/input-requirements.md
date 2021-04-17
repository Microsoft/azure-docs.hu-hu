---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: lajanuar
ms.openlocfilehash: dfd2b32094aae06675ea8ee9157370f9d2833e91
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518225"
---
Form Recognizer a következő követelményeknek megfelelő bemeneti dokumentumokon működik:

* A formátumnak JPG, PNG, PDF (szöveg vagy beolvasott) vagy TIFF formátumúnak kell lennie. A szövegbe ágyazott PDF-ek a legjobbak, mert a karakterkinyerés és a hely nem hibás.
* A fájl méretének 50 MB-nál kisebbnek kell lennie.
* A képméretnek 50 x 50 és 10000 x 10000 képpont között kell lennie.
* A PDF-méreteknek legalább 17 x 17 hüvelyknek kell lenniük, ami jogi vagy A3 méretűnek és kisebbnek kell lennie.
* PDF és TIFF esetén csak az első 200 oldal lesz feldolgozva (ingyenes szintű előfizetés esetén csak az első két oldal lesz feldolgozva).
* A betanítás adatkészletének teljes méretének legalább 500 oldalnak kell lennie.
* Ha a PDF-fájlok zárolva vannak a jelszóban, el kell távolítania a zárolást, mielőtt beküldi azokat.
* Papírdokumentumok vizsgálata esetén az űrlapoknak kiváló minőségűnek kell lennie.
* A nem felügyelet nélküli (címkével jelölt adatok nélküli) tanuláshoz az adatoknak kulcsokat és értékeket kell tartalmazni.
* A nem felügyelet nélküli (címkével jelölt adatok nélküli) tanuláshoz a kulcsoknak az értékek felett vagy bal oldalon kell megjelenniük; nem jelennek meg a jobb vagy lenti alatt.
