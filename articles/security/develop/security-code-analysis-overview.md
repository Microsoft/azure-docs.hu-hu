---
title: A Microsoft biztonsági kód elemzésének dokumentációja – áttekintés
description: További információ a Microsoft biztonsági kód elemzése bővítményről. Ezzel a bővítménnyel hozzáadhat biztonsági kód-elemzéseket az Azure DevOps CI/ID-folyamatokhoz.
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
ms.openlocfilehash: 4443bdbc134f222256c6af8bb1f52c684cdb4624
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801196"
---
# <a name="about-microsoft-security-code-analysis"></a>Tudnivalók a Microsoft biztonsági kódok elemzéséről

> [!Note]
> 2022. március 1-től érvényes a Microsoft biztonsági kód elemzése (MSCA) bővítmény kivonásra kerül. A meglévő MSCA-ügyfelek a 2022. március 1-től megőrzik a MSCA való hozzáférésüket. Az Azure DevOps alternatív lehetőségeiért tekintse meg a [OWASP forráskódját elemző eszközöket](https://owasp.org/www-community/Source_Code_Analysis_Tools) . Azon ügyfelek számára, akik a GitHubra kívánnak áttelepítést végezni, megnézheti a [GitHub fokozott biztonságát](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

A Microsoft biztonsági kód elemzése bővítménnyel a csapatok hozzáadhatják a biztonsági kód elemzését az Azure DevOps folyamatos integrációs és szállítási (CI/CD) folyamataihoz. Ezt az elemzést a [biztonságos fejlesztési életciklus (SDL)](https://www.microsoft.com/securityengineering/sdl/practices) szakértői a Microsoftnál ajánljuk.

Az egységes UX a futó eszközök összetettségének elrejtésével leegyszerűsíti a biztonságot. Az eszközök NuGet-alapú kézbesítésével a csapatoknak már nincs szükségük a szerszámozás telepítésének vagy frissítésének kezeléséhez. A felépítési feladatok esetében mind a parancssori, mind a alapszintű illesztőfelületek képesek az eszközök minél több feletti vezérlésére.

A csapatok olyan hatékony utófeldolgozó képességeket is használhatnak, mint például a következők:

- Naplók közzététele a megőrzéshez.
- Kezelhető, fejlesztő által irányított jelentések generálása.
- A Build-megszakítások beállítása regressziós teszteken.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Miért érdemes a Microsoft biztonsági kód elemzését használni?

### <a name="security-simplified"></a>A biztonság egyszerűsített

A Microsoft biztonsági kód-elemzési eszközeinek az Azure DevOps-folyamathoz való hozzáadása olyan egyszerű, mint az új feladatok hozzáadása. Szabja testre a feladatokat, vagy használja az alapértelmezett viselkedését. A feladatok az Azure DevOps-folyamat részeként futnak, és olyan naplókat készítenek, amelyek számos különböző eredményt részleteznek.

### <a name="clean-builds"></a>Tiszta buildek

Az eszközök által jelentett kezdeti problémák kezelése után beállíthatja, hogy a bővítmény új problémák esetén is kibontsa a buildeket. A folyamatos integrációs buildek beállítása minden lekéréses kérelem esetében egyszerű.

### <a name="set-it-and-forget-it"></a>Beállítás és felejtsd el

Alapértelmezés szerint a Build-feladatok és-eszközök naprakészek maradnak. Ha egy eszköz frissített verziója van telepítve, nem kell letöltenie és telepítenie. A bővítmény gondoskodik a frissítésről.

### <a name="under-the-hood"></a>technikai részletek

A bővítmény felépítési feladatai az alábbiak bonyolultságát rejtik el:
  - A Security static-Analysis Tools futtatása.
  - A naplófájlokból származó eredmények feldolgozásával hozzon létre egy összegző jelentést, vagy szakítsa meg a buildet.

## <a name="microsoft-security-code-analysis-tool-set"></a>Microsoft biztonsági kód Analysis Tool set

A Microsoft biztonsági kód elemzése bővítmény a fontos elemzési eszközök legújabb verzióit teszi elérhetővé az Ön számára. A bővítmény a Microsoft által felügyelt eszközöket és a nyílt forráskódú eszközöket is tartalmazza.

Ezeket az eszközöket a rendszer automatikusan letölti a felhőalapú ügynökre, miután a megfelelő felépítési feladatot használja a folyamat konfigurálásához és futtatásához.

Ez a szakasz felsorolja a bővítményben jelenleg elérhető eszközök készletét. Tekintse meg a további eszközök hozzáadását. Emellett küldje el nekünk javaslatait a hozzáadni kívánt eszközökhöz.

### <a name="anti-malware-scanner"></a>Kártevő szoftver víruskereső

A kártevő-elhárító képolvasó felépítési feladata mostantól a Microsoft biztonsági kód elemzése bővítmény részét képezi. Ezt a feladatot olyan Build-ügynökön kell futtatni, amelyen már telepítve van a Windows Defender. További információt a [Windows Defender webhelyén](https://aka.ms/defender)talál.

### <a name="binskim"></a>BinSkim

A BinSkim egy hordozható végrehajtható fájl (PE), amely a fordítóprogram beállításait, a kapcsolati beállításokat és a bináris fájlok egyéb biztonsági jellemzőit ellenőrzi. Ez a felépítési feladat parancssori burkolót biztosít a binskim.exe Console alkalmazás köré. A BinSkim egy nyílt forráskódú eszköz. További információ: [BinSkim a githubon](https://github.com/Microsoft/binskim).

### <a name="credential-scanner"></a>Hitelesítőadat-olvasó

A forráskódban tárolt jelszavak és egyéb titkos kódok jelentős problémát jelentenek. A hitelesítő adatok szkennere egy szabadalmaztatott statikus elemzési eszköz, amely segít a probléma megoldásában. Az eszköz a forráskódban és a Build kimenetében észleli a hitelesítő adatokat, a titkos kulcsokat, a tanúsítványokat és a bizalmas tartalmakat.

### <a name="roslyn-analyzers"></a>-Elemzők

A a a Microsoft fordítói szolgáltatásának integrált eszköze a felügyelt C# és a Visual Basic kód statikus elemzéséhez. További információkért lásd: a [-alapú elemzők](/dotnet/fundamentals/code-analysis/quality-rules/security-warnings).

### <a name="tslint"></a>TSLint

A TSLint egy bővíthető, statikus elemzési eszköz, amely a működés közbeni olvashatóságot, karbantartást és hibákat is ellenőrzi. A modern szerkesztők és a Build rendszerek széles körben támogatottak. Testre szabhatja a saját, a konfigurációt és a formázó alakzatokat. A TSLint egy nyílt forráskódú eszköz. További információ: [TSLint a githubon](https://github.com/palantir/tslint).

## <a name="analysis-and-post-processing-of-results"></a>Az eredmények elemzése és utólagos feldolgozása

A Microsoft biztonsági kód elemzése bővítmény három utófeldolgozó feladattal is rendelkezik. Ezek a feladatok segítenek elemezni a biztonság-eszköz feladatok által talált eredményeket. Egy folyamathoz való hozzáadáskor ezek a feladatok általában az összes többi eszköz feladatait követik.

### <a name="publish-security-analysis-logs"></a>Biztonsági elemzési naplók közzététele

A biztonsági elemzési naplók közzététele felépítési feladat megőrzi a Build során futtatott biztonsági eszközök naplófájljait. Ezeket a naplókat a vizsgálathoz és a követéshez is elolvashatja.

A naplófájlokat egy. zip-fájlként teheti közzé az Azure-összetevőkben. Azt is megteheti, hogy átmásolja őket egy elérhető fájlmegosztás számára a saját Build-ügynökből.

### <a name="security-report"></a>Biztonsági jelentés

A biztonsági jelentés létrehozása feladat elemzi a naplófájlokat. Ezeket a fájlokat a Build során futó biztonsági eszközök hozzák létre. A Build feladat Ezután létrehoz egy összefoglaló jelentési fájlt. Ez a fájl az Analysis Tools által talált összes problémát megjeleníti.

Ezt a feladatot beállíthatja úgy, hogy az adott eszközökre vagy az összes eszközre vonatkozó eredményeket jelentsen. Kiválaszthatja azt is, hogy milyen problémát jelentsen a jelentés, például a hibák, illetve a hibák és a figyelmeztetések.

### <a name="post-analysis-build-break"></a>Elemzés utáni (Build Break)

Az elemzés utáni Build feladattal olyan Build-megszakítást adhat meg, amely szándékosan egy buildet eredményez. Ha egy vagy több elemzési eszköz hibát jelent a kódban, be kell szúrnia egy összeállítási megszakítást.

Ezt a feladatot úgy is beállíthatja, hogy az adott eszközök vagy az összes eszköz által talált problémákra kibontsa a buildet. Azt is megteheti, hogy a talált problémák súlyossága (például hibák vagy figyelmeztetések) alapján van-e konfigurálva.

>[!NOTE]
>A tervezés szerint az egyes felépítési feladatok sikeresek lesznek, ha a feladat sikeresen befejeződik. Ez igaz, hogy az eszköz hibákat észlel-e, így az összes eszköz futtatásához a Build a befejezéshez is futtatható.

## <a name="next-steps"></a>Következő lépések

A Microsoft biztonsági kódok elemzésének bevezetésével és telepítésével kapcsolatos útmutatásért tekintse meg a bevezetési [és telepítési útmutatót](security-code-analysis-onboard.md).

A Build-feladatok konfigurálásával kapcsolatos további információkért tekintse meg a [konfigurációs útmutatót](security-code-analysis-customize.md) vagy a [YAML konfigurációs útmutatóját](yaml-configuration.md).

Ha további kérdései vannak a bővítményről és a rendelkezésre álló eszközökről, tekintse meg a [Gyakori kérdések oldalát](security-code-analysis-faq.md).