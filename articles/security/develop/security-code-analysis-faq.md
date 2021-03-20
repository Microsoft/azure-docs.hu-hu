---
title: A Microsoft biztonsági kód elemzésének dokumentációja – GYIK
description: Ismerje meg a Microsoft biztonsági kód elemzése bővítményt a gyakori kérdések (GYIK) áttekintésével.
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
ms.openlocfilehash: b851e52141cf93aba4aab9b2768658f0aac4660f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879915"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések
Kérdése van? További információért tekintse meg az alábbi gyakori kérdéseket.

## <a name="general-faq"></a>Gyakori kérdések – általános

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Telepíthetem a bővítményt a Visual Studio Team Foundation Server példányára egy Azure DevOps-példány helyett?

Nem. A bővítmény nem érhető el a Visual Studio Team Foundation Server letöltéséhez és telepítéséhez.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Futtatni kell a Microsoft biztonsági kód elemzését a buildtel? 

Talán. Ez az Analysis Tool típustól függ. Lehetséges, hogy a forráskód csak az egyetlen szükséges dolog, vagy a létrehozási kimenet szükséges.

Például a hitelesítőadat-olvasó (CredScan) elemzi a fájlokat a tárház mappastruktúrát. Az elemzés miatt futtathatja a CredScan, és közzéteheti a biztonsági elemzési naplókat egy önálló buildben az eredmények eléréséhez.

Az olyan egyéb eszközökhöz, mint a BinSkim, amelyek a Build utáni összetevők elemzését végzik, először a Build szükséges.

### <a name="can-i-break-my-build-when-results-are-found"></a>Megtörhetem a buildet, ha az eredmények megtalálhatók?

Igen. Ha bármilyen eszköz problémát vagy hibát jelez a naplófájljában, bevezetheti a létrehozási megszakítást. Csak adja hozzá az elemzés utáni Build feladatot, és jelölje be a jelölőnégyzetet minden olyan eszközhöz, amelyre el szeretné törni a buildet.

Az elemzés utáni feladat felhasználói felületén megadhatja, hogy a buildet megszakítsa, ha bármely eszköz csak a hibákat vagy a hibákat és a figyelmeztetéseket jelenti.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Hogyan különböznek az Azure DevOps parancssori argumentumai a különálló asztali eszközökben lévő argumentumokkal? 

A legtöbb esetben az Azure DevOps felépítési feladatai a biztonsági eszközök parancssori argumentumai körüli közvetlen burkolók. Átadhat argumentumként egy felépítési feladatnak, amelyet általában egy parancssori eszköznek továbbít.

Észrevehető különbségek:

- Az eszközök az ügynök $ (Build. SourcesDirectory) vagy a (z)% BUILD_SOURCESDIRECTORY% forrás mappájából futnak. Példa a C:\agent \_ work\1\s.
- Az argumentumok elérési útjai a korábban felsorolt forrás könyvtár gyökeréhez képest relatívak lehetnek. Az elérési utak is lehetnek abszolútak. Az abszolút elérési utakat az Azure DevOps Build változók használatával vagy egy helyszíni ügynök futtatásával, a helyi erőforrások ismert telepítési helyeivel érheti el.
- Az eszközök automatikusan biztosítják a kimeneti fájl elérési útját vagy mappáját. Ha kimeneti helyet ad meg egy felépítési feladathoz, a rendszer lecseréli a helyet a naplófájlok jól ismert helyének elérési útjára a Build-ügynökön.
- Néhány további parancssori argumentum módosul bizonyos eszközök esetében. Az egyik példa a grafikus felhasználói felület indítását biztosító beállítások hozzáadása vagy eltávolítása.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Futtathatok olyan felépítési feladatot, mint például a hitelesítőadat-olvasó több tárházban egy Azure DevOps-Builden?

Nem. A biztonságos fejlesztői eszközök egyetlen folyamaton belüli több tárházban való futtatása nem támogatott.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>A megadott kimeneti fájl nem jön létre, vagy nem találom a megadott kimeneti fájlt

A felépítési feladatok szűrnek néhány felhasználói adatot. Erre a kérdésre konkrétan a létrehozott kimeneti fájl helyét frissíti, hogy az a Build ügynök közös helye legyen. További információ erről a helyről: az alábbi kérdések.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Hol jönnek létre a mentett eszközök által létrehozott kimeneti fájlok? 

A Build Tasks automatikusan hozzáadja a kimeneti elérési utakat ehhez a jól ismert helyhez a Build-ügynökön: $ (Agent. BuildDirectory) \_ sdt\logs. Mivel ezen a helyen szabványosítjuk az adatokat, a Code-Analysis naplókat előállító vagy használó csapatok hozzáférhetnek a kimenethez.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Várólistára helyezhetek egy buildet a feladatok futtatásához egy üzemeltetett Build-ügynökön? 

Igen. A bővítmény összes feladatát és eszközét üzemeltetett Build-ügynökön lehet végrehajtani.

>[!NOTE]
> A kártevő szoftverek elleni ellenőrzőeszköz felépítési feladatához szükség van egy Build ügynökre, amelyen engedélyezve van a Windows Defender. Az üzemeltetett Visual Studio 2017-es és újabb verziói biztosítják ezt az ügynököt. A Build feladat nem fut a Visual Studio 2015 üzemeltetett ügynökön.
>
> Bár az aláírások nem frissíthetők ezen az ügynökökön, az aláírásoknak mindig kevesebbnek kell lenniük, mint három órával régebbiek.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Futtathatom ezeket a felépítési feladatokat egy kiadási folyamat részeként egy build-folyamat helyett?

A legtöbb esetben igen.

Az Azure DevOps azonban nem támogatja a kiadási folyamatokon belüli feladatok futtatását, amikor ezek a feladatok az összetevőket teszik közzé. Ez a támogatás hiánya megakadályozza, hogy a közzétételi biztonsági elemzési naplók feladat sikeresen fusson egy kiadási folyamatban. A feladat Ehelyett egy leíró hibaüzenettel meghiúsul.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Honnan töltheti le az eszközöket a Build-feladatok közül?

A felépítési feladatok letölthetik az eszközök NuGet csomagjait az [Azure DevOps Package Management-hírcsatornából](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json). A felépítési feladatok a Node csomagkezelő kezelőjét is használhatják, amelyet előre kell telepíteni a Build-ügynökön. Ilyen telepítés például a **NPM telepítése tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Milyen hatással van a bővítmény telepítése az Azure DevOps-szervezetre? 

A telepítéskor a bővítmény által biztosított biztonsági Build-feladatok a szervezet összes felhasználója számára elérhetővé válnak. Azure-folyamat létrehozásakor vagy szerkesztésekor ezek a feladatok a Build-Task gyűjtemény listából érhetők el. Ellenkező esetben a bővítmény Azure DevOps-szervezetben való telepítése nincs hatással. A telepítés nem módosítja a Fiókbeállítások, a projekt beállításait vagy a folyamatokat.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>Módosítja a bővítményt a meglévő Azure-folyamatokkal? 

Nem. A bővítmény telepítése lehetővé teszi a biztonsági Build-feladatok elérhetővé tételét a folyamatokon kívül. A Build-definíciók hozzáadására vagy frissítésére továbbra is szükség van, hogy az eszközök működjenek a létrehozási folyamattal.

## <a name="task-specific-faq"></a>Feladat-specifikus gyakori kérdések

A felépítési feladatokra vonatkozó kérdések ebben a szakaszban találhatók.

### <a name="credential-scanner"></a>Hitelesítőadat-olvasó

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>Mik azok a gyakori elnyomási forgatókönyvek és példák?

Az alábbiakban a leggyakoribb elnyomási forgatókönyvek közül kettőt olvashat.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Egy adott titok összes előfordulásának letiltása a megadott elérési úton

A CredScan kimeneti fájljából származó titkos kód kivonatának kulcsát az alábbi mintában látható módon kell megadnia.

```js
{
    "tool": "Credential Scanner",
    "suppressions": [
    {
        "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
        "_justification": "Secret used by MSDN sample, it is fake."
    }
  ]
}
```

>[!WARNING]
> A kivonatoló kulcsot a rendszer az egyező érték vagy a fájl tartalmának egy részével hozza létre. Bármely forráskód-változat megváltoztathatja a kivonatoló kulcsot, és letilthatja az elnyomási szabályt.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Egy adott fájl összes titkának letiltásához, vagy a titkos fájl mellőzéséhez

A fájl kifejezés lehet fájlnév. Egy teljes fájl elérési útjának vagy fájlnevének basename része is lehet. Helyettesítő karakterek nem használhatók.

Az alábbi példák bemutatják, hogyan lehet letiltani a fájlt \<InputPath>\src\JS\lib\angular.js

Példák az érvényes letiltási szabályokra:

- \<InputPath>\src\JS\lib\angular.js – letiltja a fájlt a megadott elérési úton
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js – azonos nevű fájl letiltása

```js
{
    "tool": "Credential Scanner",
    "suppressions": [
    {
        "file": "\\files\\AdditonalSearcher.xml", 
        "_justification": "Additional CredScan searcher specific to my team"
    },
    {
        "file": "\\files\\unittest.pfx", 
        "_justification": "Legitimate UT certificate file with private key"
    }
  ]
}
```

>[!WARNING] 
> A rendszer a fájlhoz hozzáadott összes jövőbeli titkot is automatikusan letiltja.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Mik azok a javasolt irányelvek a titkok kezeléséhez?

A következő források segítenek a titkok biztonságos kezelésében és a bizalmas adatok elérésében az alkalmazásokon belül:

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../azure-sql/database/authentication-aad-overview.md)
 - [Azure AD-Managed Service Identity (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Azure-erőforrások felügyelt identitásai](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Felügyelt identitások Azure App Service és Azure Functions](../../app-service/overview-managed-identity.md)
 - [AppAuthentication-könyvtár](/dotnet/api/overview/azure/service-to-service-authentication)


További információkért tekintse meg a [titkok biztonságos kezelését a felhőben](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/)című blogbejegyzésben.

#### <a name="can-i-write-my-own-custom-searchers"></a>Írhatok saját egyéni keresőket?

A hitelesítő adatok beolvasása a buildsearchers.xml fájlban általában definiált tartalomszolgáltatókon alapul. A fájl a **ContentSearcher** OBJEKTUMOT képviselő XML szerializált objektumok tömbjét tartalmazza. A program kiosztása jól tesztelt keresők készletével történik. Saját egyéni keresőket is megvalósíthat.

A tartalmi kereső a következőképpen van definiálva:

- **Name (név**): a hitelesítőadat-olvasó kimeneti fájljaiban használni kívánt leíró kereső neve. Javasoljuk, hogy a keresőmotor neveként a teve-Case elnevezési konvenciót használja.
- **RuleId**: a kereső stabil átlátszatlan azonosítója:
    - A hitelesítő adatok képolvasó alapértelmezett keresője egy **RuleId** , például a CSCAN0010, a CSCAN0020 vagy a CSCAN0030 értékhez van rendelve. Az utolsó számjegy a keresési csoportok lehetséges egyesítésére és a reguláris kifejezések (regex) használatával való felosztására van fenntartva.
    - A testreszabott kereső **RuleId** értékének saját névtérrel kell rendelkeznie. Ilyenek például a következők: CSCAN- \<Namespace\> 0010, CSCAN- \<Namespace\> 0020 és CSCAN- \<Namespace\> 0030.
    - A teljes kereső neve egy **RuleId** érték és egy kereső neve kombinációja. Ilyenek például a CSCAN0010. KeyStoreFiles és CSCAN0020. Base64EncodedCertificate.
- **ResourceMatchPattern**: fájlkiterjesztések a keresővel való kereséshez.
- **ContentSearchPatterns**: karakterláncok tömbje, amely a megfelelő regex-utasításokat tartalmazza. Ha nincs megadva keresési minta, a rendszer az **ResourceMatchPattern** értékkel egyező összes fájlt visszaadja.
- **ContentSearchFilters**: a keresőmotor-specifikus hamis pozitív értékek szűrésére szolgáló regex-utasításokat tartalmazó karakterláncok tömbje.
- **MatchDetails**: leíró üzenet, enyhítő utasítások vagy mindkettő, amelyet hozzá kell adni a kereső minden egyeztetéséhez.
- **Javaslat**: a javaslatok mezőinek tartalma az előgyors jelentési formátum használatával.
- **Súlyosság**: egész szám, amely egy probléma súlyossági szintjét tükrözi. A legmagasabb súlyossági szint értéke 1.

  ![A hitelesítő adatok képolvasó-telepítőjét ábrázoló XML](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>-Elemzők

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Mik azok a gyakori hibák, amikor a-elemzők feladatait használják?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>A projekt nem megfelelő Microsoft.NETCore.App-verzióval lett visszaállítva

A teljes hibaüzenet:

"Hiba: a projekt a Microsoft.NETCore.App *x. x. x* verziójának használatával lett visszaállítva, de az aktuális beállításokkal az *y. y. y* verziója lesz használva. A probléma megoldásához ellenőrizze, hogy ugyanazokat a beállításokat használja-e a rendszer a visszaállításhoz és a későbbi műveletekhez, például a létrehozáshoz vagy a közzétételhez. Ez a probléma általában akkor fordulhat elő, ha a RuntimeIdentifier tulajdonságot a Build vagy a publish elemnél állítja be, de a visszaállítás során nem. "

Mivel a (z) a (z) a gyűjtemény részeként futtatott a a-elemzők, a kiépíthető állapotban kell lennie a forrás fájának.

Előfordulhat, hogy a fő Build és a a a a a a a a a a a a a (a)-elemzők lépései között a forrás fát olyan állapotba helyezi Ezt az extra lépést valószínűleg **dotnet.exe közzétenni**. Próbálja megismételni azt a lépést, amely egy NuGet-visszaállítást végez közvetlenül a a a-elemzők lépése előtt. Ez a duplikált lépés felépíthető állapotba helyezheti a forrás faszerkezetét.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe nem hozható létre elemző példány

A teljes hibaüzenet:

A (z) "" csc.exe "kilépett a következő hibakóddal:" az Analyzer *AAAA* -példánya nem hozható létre a C: \\ *BBBB*. dll fájlból: nem sikerült betölteni a (z) "Microsoft. CodeAnalysis, Version =*x. x*. x. x, Culture = semleges, PublicKeyToken = 31bf3856ad364e35" vagy annak egyik függőségét. A megadott fájl nem található. "

Győződjön meg arról, hogy a fordító támogatja a a a a a a "a" A parancs futtatásához **csc.exe (Version** ) 2,6-es vagy újabb verziószámot kell jelentenie.

Előfordulhat, hogy egy. csproj-fájl felülbírálja a Build Machine Visual Studio telepítését a Microsoft.Net. Compilers csomagból való hivatkozással. Ha nem kívánja használni a fordító egy adott verzióját, távolítsa el a Microsoft.Net. Compilers mutató hivatkozásait. Ellenkező esetben győződjön meg arról, hogy a hivatkozott csomag verziószáma 2,6 vagy újabb.

Próbálja meg lekérni a hibanapló elérési útját, amely a **csc.exe/errorlog** beállításban van megadva. A beállítás és az elérési út megjelenik a következő naplóban: a-elemzők felépítési feladata. Ilyenek például a **/errorlog: f:\ts-services-123 \_ work\456\s\Some\Project\Code\Code.csproj.Sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>A C# fordítóprogram verziója nem elég közelmúltbeli

A C# fordító legújabb verzióinak beszerzéséhez nyissa meg a [Microsoft.net. compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers)programot. A telepített verziójának lekéréséhez futtassa a következő parancsot a parancssorban: **csc.exe Version** . Győződjön meg arról, hogy a 2,6-es vagy újabb verziójú Microsoft.Net. Compilers NuGet-csomagra hivatkozik.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>Az MSBuild és a VSBuild naplók nem találhatók

A DevOps-elemzők létrehozási feladatának le kell kérdezni az MSBuild-napló Azure-beli az MSBuild-Build feladatból. Ha az analizátor feladat közvetlenül az MSBuild feladat után fut, a napló még nem lesz elérhető. Helyezzen el más feladatokat az MSBuild-feladat és a a a a a a a a a a a a a a ( További feladatok például a BinSkim és a kártevők elleni képolvasó.

## <a name="next-steps"></a>Következő lépések

Ha további segítségre van szüksége, a Microsoft biztonsági kód elemzésének támogatása hétfőtől péntekig 9:00-tól 5:00-ig, a csendes-óceáni téli időpontig érhető el.

- Előkészítés: Tekintse meg a bevezetési [dokumentációt](security-code-analysis-onboard.md)
  
- Támogatás: e-mail-cím a [Microsoft biztonsági kódjának elemzését támogató](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request) csapatnál