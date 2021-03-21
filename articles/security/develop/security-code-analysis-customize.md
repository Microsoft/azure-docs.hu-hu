---
title: A Microsoft biztonsági kód elemzési feladatainak testreszabása
titleSuffix: Azure
description: Ez a cikk a Microsoft biztonsági kód elemzése bővítmény feladatainak testreszabását ismerteti
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 4016e1dd055b45f9cd59a172d0e71ef95fec1c40
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008684"
---
# <a name="configure-and-customize-the-build-tasks"></a>A Build-feladatok konfigurálása és testreszabása

Ez a cikk részletesen ismerteti az egyes összeállítási feladatokban elérhető konfigurációs beállításokat. A cikk a biztonsági kód elemzési eszközeinek feladataival kezdődik. A feldolgozás utáni feladatokkal végződik.

## <a name="anti-malware-scanner-task"></a>Kártevők elleni víruskereső feladat

>[!NOTE]
> A kártevő szoftverek elleni ellenőrzőeszköz felépítési feladatához szükség van egy Build ügynökre, amelyen engedélyezve van a Windows Defender. Az üzemeltetett Visual Studio 2017-es és újabb verziói biztosítják ezt az ügynököt. A Build feladat nem fut a Visual Studio 2015 üzemeltetett ügynökön.
>
> Bár az aláírások nem frissíthetők ezen az ügynökökön, az aláírásoknak mindig kevesebbnek kell lenniük, mint három órával régebbiek.

A feladat konfigurációjának részletei a következő képernyőképen és szövegen láthatók.

![A kártevő-elhárító képolvasó felépítési feladatának konfigurálása](./media/security-tools/5-add-anti-malware-task600.png)

A képernyőkép **típus** listájában válassza az **alapszintű** lehetőséget. Válassza az **Egyéni** lehetőséget a vizsgálat testreszabására szolgáló parancssori argumentumok megadásához.

A Windows Defender az Windows Update ügyfelet használja az aláírások letöltésére és telepítésére. Ha az aláírás frissítése sikertelen a Build-ügynökön, a **HRESULT** valószínűleg Windows Update származik.

A Windows Update hibákkal és azok enyhítésével kapcsolatos további információkért lásd: [Windows Update hibakódok összetevő szerint](/windows/deployment/update/windows-update-error-reference) és a TechNet [-cikk Windows Update ügynök – hibakódok](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

A feladathoz tartozó YAML-konfigurációval kapcsolatos információkért tekintse meg a [kártevők elleni YAML kapcsolatos lehetőségeket](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>BinSkim feladat

> [!NOTE]
> A BinSkim feladat futtatása előtt a buildnek meg kell felelnie az alábbi feltételek egyikének:
>  - A Build bináris összetevőket hoz létre a felügyelt kódból.
>  - Olyan bináris összetevők vannak véglegesítve, amelyeket a BinSkim-mel kíván elemezni.

A feladat konfigurációjának részletei a következő képernyőképen és listában láthatók.

![A BinSkim-létrehozási feladat konfigurálása](./media/security-tools/7-binskim-task-details.png)

- Állítsa a Build konfigurációját hibakeresésre, hogy a. pdb hibakeresési fájljait hozza létre. A BinSkim ezeket a fájlokat használja a kimeneti bináris fájljaiban felmerülő problémák a forráskódba való visszaállításához.
- A saját parancssorok keresésének és létrehozásának elkerüléséhez tegye a következőket:
     - A **típus** listában válassza az **alapszintű** lehetőséget.
     - A **függvény** listából válassza az **elemzés** lehetőséget.
- A **cél** mezőben adjon meg egy vagy több megjelölést egy fájlhoz, könyvtárhoz vagy szűrési mintához. Ezek a megadott beállítások egy vagy több elemzett bináris fájlra oldhatók fel:
    - Több megadott célpontot pontosvesszővel kell elválasztani (;).
    - A megadott elem lehet egyetlen fájl, vagy helyettesítő karakter is szerepelhet.
    - A könyvtár specifikációinak mindig a * értékkel kell végződnie \\ .
    - Angol nyelvű Példák:

```binskim-targets
           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
```

- Ha a **Type (típus** ) listában kiválasztja a **parancssort** , binskim.exe kell futtatnia:
     - Győződjön meg arról, hogy az binskim.exe első argumentumai a műveletek **elemzését** , majd egy vagy több elérésiút-specifikációt követnek. Minden elérési út lehet teljes elérési út vagy a forrás könyvtárhoz viszonyított elérési út.
     - A célként megadott elérési utakat szóközzel kell elválasztani.
     - Kihagyhatja az **/o** vagy a **/output** beállítást. A kimeneti érték hozzá lesz adva vagy lecserélve.
     - A standard parancssori konfigurációk a következőképpen láthatók.

```binskim-line-args
           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
```

> [!NOTE]
> A záró \\ * fontos, ha a célként megadott könyvtárakat ad meg.

További információ a BinSkim parancssori argumentumokról, a szabályok azonosító alapján vagy kilépési kódokról: [BinSkim felhasználói útmutató](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

A feladathoz tartozó YAML-konfigurációval kapcsolatos információkért tekintse meg a [BINSKIM YAML-beállításait](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>Hitelesítőadat-olvasó feladat

A feladat konfigurációjának részletei a következő képernyőképen és listában láthatók.

![A hitelesítőadat-képolvasó felépítési feladatának konfigurálása](./media/security-tools/3-taskdetails.png)

Az elérhető lehetőségek a következők:
  - **Megjelenítendő név**: az Azure DevOps feladat neve. Az alapértelmezett érték a hitelesítőadat-olvasó futtatása
  - **Eszköz főverziója**: az elérhető értékek közé tartozik a **CredScan v2**, a **CredScan v1**. Javasoljuk, hogy az ügyfelek a **CredScan v2** verzióját használják.
  - **Kimeneti formátum**: a rendelkezésre álló értékek a **TSV**, a **CSV**, a **SARIF** és az **előgyorsak**.
  - **Eszköz verziója**: javasoljuk, hogy válassza a **legutóbbi** lehetőséget.
  - **Vizsgálati mappa**: a beolvasandó adattár mappája.
  - **Keresők** fájltípusa: a vizsgálathoz használt kereső fájl megkeresésének lehetőségei.
  - **Mellőzési fájl**: egy [JSON](https://json.org/) -fájl letiltja a kimeneti napló hibáit. A letiltási forgatókönyvekkel kapcsolatos további információkért tekintse meg a jelen cikk gyakori kérdések című szakaszát.
  - **Részletes kimenet**: magától értetődő.
  - **Batch mérete**: a hitelesítő adatok beolvasójának futtatásához használt egyidejű szálak száma. Az alapértelmezett érték 20. A lehetséges értékek 1 és 2 147 483 647 közötti tartományba esnek.
  - **Egyeztetési időkorlát**: az az időtartam másodpercben, ameddig a keresőnek meg kell felelnie az ellenőrzések elhagyása előtt.
  - **Fájl vizsgálata olvasási puffer mérete**: a tartalom olvasásakor használt puffer mérete bájtban megadva. Az alapértelmezett érték a 524 288.  
  - **Maximális fájl vizsgálatának olvasási bájtjai**: egy fájlból beolvasott bájtok maximális száma a tartalom elemzése során. Az alapértelmezett érték a 104 857 600.
  - **Vezérlési beállítások**  >  A **feladat futtatása**: Megadja, hogy a feladat Mikor fusson. Összetettebb feltételek megadásához válassza az **Egyéni feltételek** lehetőséget.
  - **Verzió**: a Build feladat verziója az Azure DevOps-n belül. Ez a beállítás nem gyakran használatos.

A feladathoz tartozó YAML-konfigurációval kapcsolatos információkért tekintse meg a [hitelesítő adatok szkennerének YAML beállításait](yaml-configuration.md#credential-scanner-task)

## <a name="roslyn-analyzers-task"></a>A-elemzők feladata

> [!NOTE]
> A (z) létrehozásához a következő feltételeknek kell megfelelnie:
>
> - A Build definíciója tartalmazza a beépített MSBuild vagy VSBuild Build feladatot C# vagy Visual Basic kód fordításához. Az elemzők tevékenység a beépített feladat bemenetére és kimenetére támaszkodik, hogy az MSBuild-fordítást a (z) és a (z) által engedélyezett, a (z).
> - Az ezt az összeállítási feladatot futtató Build ügynök rendelkezik a Visual Studio 2017 15,5-es vagy újabb verziójával, hogy az a fordító 2,6-es vagy újabb verzióját használja.

A feladatok konfigurációjának részletei a következő listában láthatók, és Megjegyzés:.

Az elérhető lehetőségek a következők:

- **Szabály**: az értékek az **SDL szükségesek**, az **SDL javasolt** vagy a saját egyéni szabálykészlet.
- **Elemzők verziója**: javasoljuk, hogy válassza a **legutóbbi** lehetőséget.
- **Fordítói figyelmeztetések mellőzése fájl**: a letiltott figyelmeztető azonosítók listáját tartalmazó szövegfájl.
- **Vezérlési beállítások**  >  A **feladat futtatása**: Megadja, hogy a feladat Mikor fusson. Összetettebb feltételek megadásához válassza az **Egyéni feltételek** lehetőséget.

> [!NOTE]
>
> - A a a fordítóval integrált és csak csc.exe fordítás részeként futtatható. Ezért ehhez a feladathoz a fordításban korábban futtatott fordítói parancsnak újra kell játszania vagy újra futnia. Ezt a lekérést vagy futtatást az Azure DevOps (korábban Visual Studio Team Services) lekérdezésével végezheti el az MSBuild Build feladat naplóihoz.
>
>   Nincs más mód arra, hogy a feladat megbízhatóan lekérje az MSBuild fordítási parancssort a Build definícióból. Egy Szabadkézi szövegmező hozzáadásával engedélyezte a felhasználóknak a parancssorok megadását. Azonban nehéz lenne ezeket a parancssori vonalakat naprakészen tartani, és szinkronizálni a fő buildtel.
>
>   Az egyéni buildek használatához a parancsok teljes készletét kell visszajátszani, nem csak a fordítóprogram parancsait. Ezekben az esetekben a nem triviális vagy a megbízható adatelemzők engedélyezése.
>
> - A a a fordítóval integrált a-elemzők. Ahhoz, hogy meghívja a meghívást, a (a "a" és a "szükséges
>
>   Ez az új felépítési feladat a már létrehozott C#-projektek újrafordításával valósul meg. Az új feladat csak az MSBuild és a VSBuild felépítési feladatokat használja az eredeti feladattal azonos Build vagy Build definícióban. Ebben az esetben azonban az új feladat a a következővel rendelkező, a-t használó, a és a.
>
>   Ha az új feladat az eredeti feladattal megegyező ügynökön fut, az új tevékenység kimenete felülírja az eredeti feladat kimenetét az *s* Sources mappában. Bár a Build kimenete azonos, javasoljuk, hogy futtassa az MSBuild-et, másolja a kimenetet az összetevők előkészítési könyvtárába, majd futtassa a következőt:.

Ha további erőforrásokat kíván felderíteni a (z)-elemzők feladathoz, tekintse meg a Microsoft Docs-on végzett, a [-alapú elemzéseket](/dotnet/standard/analyzers/api-analyzer) .

A Build feladat által telepített és használt Analyzer-csomag a [Microsoft. CodeAnalysis. FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)NuGet oldalon található.

A feladathoz tartozó YAML-konfigurációval kapcsolatos további információkért tekintse meg a [YAML-elemzések a következő lehetőségek közül](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>TSLint feladat

A TSLint kapcsolatos további információkért nyissa meg a [TSLint GitHub](https://github.com/palantir/tslint)-tárházat.

>[!NOTE] 
>Az is előfordulhat, hogy a [TSLint GitHub](https://github.com/palantir/tslint) -tárház kezdőlapja azt állítja be, hogy a TSLint valamikor elavulttá válik a 2019-as verzióban. A Microsoft a [ESLint](https://github.com/eslint/eslint) vizsgálja alternatív feladatként.

A feladathoz tartozó YAML-konfigurációval kapcsolatos információkért tekintse meg a [TSLINT YAML-beállításait](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>Biztonsági elemzési naplók közzététele feladat

A feladat konfigurációjának részletei a következő képernyőképen és listában láthatók.

![A biztonsági elemzési naplók közzétételének beállítása feladat konfigurálása](./media/security-tools/9-publish-security-analsis-logs600.png)  

- Összetevő **neve**: bármely karakterlánc-azonosító.
- Összetevő **típusa**: a kijelöléstől függően közzéteheti a naplókat a Azure DevOps Server vagy a Build ügynök számára elérhető megosztott fájlon.
- **Eszközök**: dönthet úgy, hogy megőrzi a naplókat az egyes eszközökön, vagy kiválaszthatja az **összes eszközt** az összes napló megőrzéséhez.

A feladathoz tartozó YAML-konfigurációval kapcsolatos további információkért tekintse meg a [biztonsági naplók közzététele YAML beállítások](yaml-configuration.md#publish-security-analysis-logs-task) című témakört.

## <a name="security-report-task"></a>Biztonsági jelentés feladata

A biztonsági jelentések konfigurációjának részletei a következő képernyőképen és listában láthatók.

![A biztonsági jelentés létrehozási feladatának konfigurálása](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Jelentések**: válassza ki a **folyamat-konzolt**, a **TSV-fájlt** és a **HTML** -fájlformátumokat. Minden kiválasztott formátumhoz létrejön egy jelentési fájl.
- **Eszközök**: válassza ki azokat az eszközöket a Build-definícióban, amelyekhez az észlelt problémákat szeretné összefoglalni. Minden kiválasztott eszköz esetében lehetőség van kijelölni, hogy csak a hibák jelennek-e meg, vagy az összegző jelentésben a hibák és a figyelmeztetések láthatók.
- **Speciális beállítások**: Ha nincsenek naplók az egyik kiválasztott eszközhöz, dönthet úgy, hogy figyelmeztetést vagy hibát naplóz. Ha hibát naplóz, a feladat meghiúsul.
- **Alapnaplók mappa**: testreszabhatja az alapnaplók mappát, ahol a naplók találhatók. Ez a beállítás azonban általában nincs használatban.

A feladathoz tartozó YAML-konfigurációval kapcsolatos további információkért tekintse meg a [biztonsági jelentés YAML beállításait](yaml-configuration.md#security-report-task) .

## <a name="post-analysis-task"></a>Az elemzést követő feladat

A feladat konfigurációjának részletei a következő képernyőképen és listában láthatók.

![Az elemzés utáni Build feladat konfigurálása](./media/security-tools/a-post-analysis600.png)

- **Eszközök**: válassza ki azokat az eszközöket a Build-definícióban, amelyekhez feltételesen szeretne beszúrni egy Build-megszakítást. Minden kiválasztott eszköz esetében lehetőség van annak kiválasztására, hogy csak a hibákat vagy a hibákat és a figyelmeztetéseket kívánja-e megszakítani.
- **Jelentés**: igény szerint megírhatja a létrehozási megszakítást okozó eredményeket. Az eredmények az Azure DevOps-konzol ablakába és a naplófájlba íródnak.
- **Speciális beállítások**: Ha nincsenek naplók az egyik kiválasztott eszközhöz, dönthet úgy, hogy figyelmeztetést vagy hibát naplóz. Ha hibát naplóz, a feladat meghiúsul.

A feladathoz tartozó YAML-konfigurációval kapcsolatos információkért tekintse meg a [post Analysis YAML beállításait](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>Következő lépések

A YAML-alapú konfigurációval kapcsolatos információkért tekintse meg a [YAML konfigurációs útmutatóját](yaml-configuration.md).

Ha további kérdése van a biztonsági kód elemzési bővítménnyel és a rendelkezésre álló eszközökkel kapcsolatban, tekintse meg a [Gyakori kérdések oldalát](security-code-analysis-faq.md).