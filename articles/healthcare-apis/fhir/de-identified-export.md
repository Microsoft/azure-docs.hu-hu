---
title: A FHIR Azure API-hoz tartozó, de azonosított adatexportálás (előzetes verzió)
description: Ez a cikk a de azonosított exportálás beállítását és használatát ismerteti.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: matjazl
ms.openlocfilehash: 60a2a41a8005e8bd0fbc313c9a177d54df6dac5e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019188"
---
# <a name="exporting-de-identified-data-preview"></a>De azonosított adatexportálás (előzetes verzió)

> [!Note] 
> A deazonosított exportálás használatakor az eredmények az olyan tényezőktől függenek, mint az adatok, illetve az ügyfél által kiválasztott függvények. A Microsoft nem tudja kiértékelni a azonosított exportálási kimeneteket, vagy megállapítani az ügyfél használati eseteinek és megfelelőségi igényeinek elfogadhatóságát. A de azonosított exportálás nem garantált az adott jogi, szabályozási és megfelelőségi követelmények teljesítése érdekében.

A $export parancs a FHIR-kiszolgálóról származó, de azonosított adatok exportálására is használható. A névtelenítésével motort használja a [névtelenítésével FHIR eszközökről](https://github.com/microsoft/FHIR-Tools-for-Anonymization), és a lekérdezési paraméterekben a névtelenítésével konfiguráció részleteit veszi igénybe. Létrehozhatja saját névtelenítésével-konfigurációs fájlját, vagy kiindulási pontként használhatja a HIPAA Safe Harbor metódusának [minta konfigurációs fájlját](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) . 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

> [!Note] 
> A FHIR készült Azure API mostantól csak a rendszerszintű ($export) rendszerű, de azonosított exportálást támogatja.

|Lekérdezési paraméter            | Példa |Nem kötelező| Leírás|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.jsbekapcsolva|A deazonosított exportáláshoz szükséges |A konfigurációs fájl neve. Tekintse meg a konfigurációs fájlformátumot [itt](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format). Ezt a fájlt egy **névtelenítésével** nevű tárolóban kell tárolni, amely az exportálási helyként konfigurált Azure Storage-fiókban található. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Nem kötelező a azonosított exportáláshoz|Ez a konfigurációs fájl ETAG. A ETAG az Azure Storage Explorer használatával kérheti le a blob tulajdonságból.|

> [!IMPORTANT]
> Mind a nyers, mind a nem azonosított exportálási művelet az exportálási konfiguráció részeként megadott Azure Storage-fiókra is vonatkozik. Azt javasoljuk, hogy a különböző, de azonosított konfigurációnak megfelelő tárolókat használjon, és kezelje a felhasználói hozzáférést a tároló szintjén.