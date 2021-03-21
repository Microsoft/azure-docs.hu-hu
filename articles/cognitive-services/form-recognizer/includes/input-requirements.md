---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: lajanuar
ms.openlocfilehash: 324fde5198fe3469e3979cf5cd102715118a49aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467292"
---
Az űrlap-felismerő a következő követelményeknek megfelelő bemeneti dokumentumokon működik:

* A formátumnak JPG, PNG, PDF (Text vagy beolvasott) vagy TIFF formátumúnak kell lennie. A Text-Embedded PDF-fájlok a legjobbak, mert nem lehetséges a hibák kinyerése és helye.
* A fájlméretnek 50 MB-nál kisebbnek kell lennie.
* A képdimenziónak 50 x 50 képpont és 10000 x 10000 képpont közé kell esnie.
* A PDF-dimenzióknak legfeljebb 17 x 17 hüvelyknek kell lenniük, amely a jogi vagy az A3-as papírméretnek felel meg, és kisebb.
* A PDF és a TIFF esetében csak az első 200 oldal lesz feldolgozva (ingyenes rétegbeli előfizetéssel csak az első két oldal feldolgozása történik).
* A betanítási adathalmaz teljes méretének 500 vagy annál kisebbnek kell lennie.
* Ha a PDF-fájlok jelszóval vannak zárolva, a küldés előtt el kell távolítania a zárolást.
* Ha papíralapú dokumentumokból olvas be dokumentumokat, az űrlapoknak kiváló minőségű vizsgálatokat kell ellátniuk.
* A szövegnek a latin ábécét (angol karakter) kell használnia.
* A nem felügyelt tanulás (címkézett adatok nélkül) esetében az adatoknak kulcsokat és értékeket kell tartalmazniuk.
* A nem felügyelt tanulás (címkézett adatok nélkül) esetében a kulcsoknak az értékek felett vagy balra kell megjelenniük; nem jelennek meg a jobb oldalon.
