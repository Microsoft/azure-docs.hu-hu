---
title: Microsoft biztonsági kód elemzésének kiadásai
description: Ez a cikk a Microsoft biztonsági kód elemzése bővítmény közelgő kiadásait ismerteti
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 7596df66dbcbe1b7cdefab4811da7174bc83ac65
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801179"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Microsoft biztonsági kód elemzésének kiadásai és ütemterve

> [!Note]
> 2022. március 1-től érvényes a Microsoft biztonsági kód elemzése (MSCA) bővítmény kivonásra kerül. A meglévő MSCA-ügyfelek a 2022. március 1-től megőrzik a MSCA való hozzáférésüket. Az Azure DevOps alternatív lehetőségeiért tekintse meg a [OWASP forráskódját elemző eszközöket](https://owasp.org/www-community/Source_Code_Analysis_Tools) . Azon ügyfelek számára, akik a GitHubra kívánnak áttelepítést végezni, megnézheti a [GitHub fokozott biztonságát](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

A Microsoft biztonsági kód Analysis Team a Fejlesztői támogatás partnerrel együttműködve büszkén jelenti be, hogy a legújabb és a közelgő fejlesztéseket a MSCA-bővítményben mutatjuk be.


## <a name="credential-scanner-v20-released-in-april-2020"></a>Hitelesítőadat-olvasó v 2.0: megjelent április 2020

### <a name="innovations--improvements"></a>Innovációk & fejlesztések

- **Alapszintű motor**

   - 25%-os teljesítmény-frissítés a közel lineáris futási idővel
   - Kontextus/bizonyítékon alapuló keresés és rangsorolás a nagyobb pontosság érdekében
   - Az általános jelszó-észlelések és a megfelelő logika fejlesztése a nyilvánvaló helyőrzők esetében (például fakePassword)

- **Lefedettség** – a 25 + titkos típusok támogatása, beleértve a következő kért legfelső szintű támogatást:

   - Háló fiók tanúsítványának jelszava
   - Ügyfél titkos/API-kulcsa
   - HTTP-engedélyezési fejléc
   - Amazon S3-ügyfél titkos kulcs elérési kulcsa
   - Ügyfél-hozzáférési jogkivonat Azure Active Directory
   - Azure Function Master/API-kulcs
   - Power BI hozzáférési kulcs
   - Azure Resource Manager sablon jelszavának mintája

- **Kimenetek**

   - A SARIF 2,1 és a CSV-fájl kimeneti fájlformátumának támogatása

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v 1.6.0: megjelent április 2020

### <a name="improvements"></a>Fejlesztései

- SZOLGÁLTATÁS: frissítés a Final SARIF v2-re (2.1.16 verzió). Ez a frissítés lehetővé teszi az eredmények gyorsítótárazását a parancssorból történő továbbításkor, jelentős teljesítménybeli javulást jelent, ha a címtárakat a vizsgálati célok több példányát tartalmazó könyvtárak rekurzív elemzésével végzi.
- HIBAJAVÍTÁS: a BA2021 helyesírásának javítása. DoNotMarkWritableSectionsAsExecutable kimenete.
- TELJESÍTMÉNY: a PDB betöltésének megszüntetése a felügyelt szerelvények összes nem vegyes üzemmódja számára, beleértve az IL-függvénytárat (az idő előtt lefordított) bináris fájlokat.
- HAMIS negatív javítás: annak ellenőrzése, hogy egy, a bináris fájl mellé helyezett PDB az elemzés alatt álló bináris ÉRTÉKkel egyezik-e.
- SZOLGÁLTATÁS: adja meg a--local-Symbol-könyvtárak argumentumot a további (helyi, nem szimbólum-kiszolgáló) PDB kikeresési helyeinek megadásához.
- HAMIS pozitív javítás: hagyja ki a PDB-alapú elemzést a generált .NET Core Native bootstrap exe-re (amely nem felhasználó által irányítható kód).

## <a name="whats-next-in-q3-cy20"></a>Mi a következő lépés a Q3 CY20?

- Java Security Analysis Tool
- Python biztonsági elemzési eszköz
- Az írógéppel és a JavaScripthez tartozó
- Resource Manager-sablonok Analysis Tool

## <a name="tool-deprecation-notification"></a>Eszköz elavult értesítése

### <a name="microsoft-security-risk-detection-msrd-is-deprecated-on-june-26-2020"></a>A Microsoft biztonsági kockázatok észlelése (MSRD) a 26 2020-es júniusi elavult.

Az elavult MSRD-kivonási szolgáltatást egy nyílt forráskódú, saját üzemeltetésű fejlesztői fuzzy platformmal helyettesíti az Azure-hoz. Ez a platform jelenleg fejlesztés alatt áll és tesztelve van a Microsoft alapvető termékcsoportok számos tagja számára. Ez a zavaros platform integrálja a fertőtlenítő szoftvereket, és lehetővé teszi a CI/CD-folyamatokban beépített, alkalmazkodó, tanulási fuzz-teszteket, amelyekkel idővel bővülnek a szoftverek projektjei. A platform nyílt forráskódú kiadása a 2020-as utolsó felére van ütemezve.

## <a name="next-steps"></a>Következő lépések

A Microsoft biztonsági kódok elemzésének bevezetésével és telepítésével kapcsolatos útmutatásért tekintse meg a bevezetési [és telepítési útmutatót](security-code-analysis-onboard.md).

Ha további kérdései vannak a bővítményről és a rendelkezésre álló eszközökről, tekintse meg a [Gyakori kérdések oldalát](security-code-analysis-faq.md).
