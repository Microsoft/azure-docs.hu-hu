---
title: U-SQL-feladatok futtatása helyileg – Azure Data Lake U-SQL SDK
description: Ismerje meg, hogyan futtathatja és tesztelheti a U-SQL-feladatokat helyileg a parancssori és programozási felületek használatával a helyi munkaállomáson.
ms.service: data-lake-analytics
author: yanacai
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 03/01/2017
ms.openlocfilehash: 8fb60e62a63bfc4562f19d483dc84c99c37676b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215535"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>A U-SQL futtatása és tesztelése Azure Data Lake U-SQL SDK-val

Ha U-SQL-szkriptet fejleszt, akkor a helyileg futtathatja és tesztelheti a U-SQL-parancsfájlt, mielőtt elküldi a felhőbe. A Azure Data Lake egy Azure Data Lake U-SQL SDK nevű Nuget-csomagot biztosít ehhez a forgatókönyvhöz, amelyen keresztül könnyedén méretezheti az U-SQL-futtatást és-tesztelést. Az U-SQL-tesztet a CI (folyamatos integrációs) rendszerrel is integrálhatja a fordítás és a tesztelés automatizálásához.

Ha azt szeretné, hogy a helyi Futtatás és az U-SQL-parancsfájlok hibakeresése hogyan végezhető el manuálisan a GUI-eszközök használatával, akkor a Visual studióhoz készült Azure Data Lake-eszközöket használhatja. További információt [itt](data-lake-analytics-data-lake-tools-local-run.md)talál.

## <a name="install-azure-data-lake-u-sql-sdk"></a>A Azure Data Lake U-SQL SDK telepítése

A Azure Data Lake U-SQL SDK-t [itt](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) érheti el a Nuget.org webhelyen. A használata előtt meg kell győződnie arról, hogy az alábbiak szerint vannak függőségei.

### <a name="dependencies"></a>Függőségek

A Data Lake U-SQL SDK a következő függőségeket igényli:

- [Microsoft .NET Framework 4,6 vagy újabb](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 és Windows SDK 10.0.10240.0 vagy újabb (ezt a cikket a CppSDK nevezik). Kétféleképpen lehet beolvasni a CppSDK:

  - Telepítse a [Visual Studio Community Editiont](https://developer.microsoft.com/downloads/vs-thankyou). A program Files mappában a \Windows Kits\10 mappája látható – például: C:\Program Files (x86) \Windows Kits\10\. A Windows 10 SDK verzióját is megtalálja a \Windows Kits\10\Lib. Ha nem látja ezeket a mappákat, telepítse újra a Visual studiót, és győződjön meg róla, hogy a telepítés során kiválasztja a Windows 10-es SDK-t. Ha ezt a Visual Studióval telepítette, akkor a U-SQL helyi fordító automatikusan megtalálja.

    ![A Visual Studio helyi Data Lake eszközei – Windows 10 SDK futtatása](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Telepítse [Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs)alkalmazást. Az előre csomagolt Visual C++ és Windows SDK fájlok a következő címen találhatók: `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK.`

    Ebben az esetben a U-SQL helyi fordító nem találja a függőségeket automatikusan. Meg kell adnia a CppSDK elérési útját. Másolhatja a fájlokat egy másik helyre, vagy használhatja azt.

## <a name="understand-basic-concepts"></a>Az alapvető fogalmak ismertetése

### <a name="data-root"></a>Adatgyökér

Az adatgyökér mappa a helyi számítási fiók "helyi tárolója". Ez egyenértékű a Data Lake Analytics-fiók Azure Data Lake Store fiókjával. Egy másik adatgyökér-mappára való váltás ugyanúgy történik, mint egy másik áruházbeli fiókra váltás. Ha a gyakran megosztott és a különböző legfelső szintű mappákhoz is szeretne hozzáférni, a parancsfájlokban abszolút elérési utakat kell használnia. Vagy hozzon létre fájlrendszerbeli szimbolikus hivatkozásokat (például **MKLINK** NTFS rendszeren) az adatgyökér mappában, hogy a megosztott adatra mutasson.

Az adatgyökér mappa a következőt használja:

- Helyi metaadatok, például adatbázisok, táblák, táblázat értékű függvények (TVFs-EK) és szerelvények tárolása.
- Keresse meg a bemeneti és kimeneti útvonalakat, amelyek a U-SQL relatív elérési útjaiként vannak meghatározva. A relatív elérési utak használatával egyszerűbbé válik az U-SQL-projektek üzembe helyezése az Azure-ban.

### <a name="file-path-in-u-sql"></a>Fájl elérési útja U-SQL-ben

A U-SQL-parancsfájlokban relatív elérési utat és helyi abszolút elérési utat is használhat. A relatív elérési út a megadott adatgyökér-mappa elérési útjához képest relatív. Javasoljuk, hogy a "/" útvonalat használja elérésiút-elválasztó, hogy a parancsfájlok kompatibilisek legyenek a kiszolgálóoldali oldalával. Íme néhány példa a relatív elérési utakra és azok egyenértékű abszolút elérési útjaira. Ezekben a példákban a C:\LocalRunDataRoot az adatgyökér mappa.

|Relatív elérési út|Abszolút elérési út|
|-------------|-------------|
|/ABC/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|ABC/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/ABC/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Munkakönyvtár

Ha a U-SQL-parancsfájlt helyileg futtatja, a rendszer létrehoz egy munkakönyvtárat a fordítás során a jelenlegi futó könyvtár alatt. A fordítási kimeneteken kívül a helyi végrehajtáshoz szükséges futtatókörnyezeti fájlok árnyékolva lesznek a munkakönyvtárba. A munkakönyvtár gyökérkönyvtárának neve "ScopeWorkDir", és a munkakönyvtárban található fájlok a következők:

|Könyvtár/fájl|Könyvtár/fájl|Könyvtár/fájl|Definíció|Leírás|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Futtatókörnyezet-verzió kivonatoló karakterlánca|A helyi végrehajtáshoz szükséges futtatókörnyezeti fájlok árnyékmásolata|
| |Script_66AE4909AA0ED06C| |Parancsfájl neve + szkript elérési útjának kivonat-karakterlánca|Fordítási kimenetek és végrehajtási lépés naplózása|
| | |\_script \_ . ABR|Fordítóprogram kimenete|Algebra-fájl|
| | |\_ScopeCodeGen \_ . *|Fordítóprogram kimenete|Létrehozott felügyelt kód|
| | |\_ScopeCodeGenEngine \_ . *|Fordítóprogram kimenete|Generált natív kód|
| | |hivatkozott szerelvények|Szerelvény leírása|Hivatkozott szerelvények fájljai|
| | |deployed_resources|Erőforrás központi telepítése|Erőforrás-telepítési fájlok|
| | |XXXXXXXX. xxx [1.. n] \_ \* . *|Végrehajtási napló|Végrehajtási lépések naplózása|

## <a name="use-the-sdk-from-the-command-line"></a>Az SDK használata a parancssorból

### <a name="command-line-interface-of-the-helper-application"></a>A segítő alkalmazás parancssori felülete

Az SDK-directory\build\runtime alatt a LocalRunHelper.exe a parancssori segítő alkalmazás, amely a leggyakrabban használt helyi futtatási függvények többségéhez biztosít felületet. Vegye figyelembe, hogy a parancs és az argumentum kapcsolók is megkülönböztetik a kis-és nagybetűket. A következő meghívása:

```console
LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]
```

LocalRunHelper.exe futtatása argumentumok nélkül vagy a **Súgó** kapcsolóval a Súgó információinak megjelenítéséhez:

```console
> LocalRunHelper.exe help
    Command 'help' :  Show usage information
    Command 'compile' :  Compile the script
    Required Arguments :
        -Script param
                Script File Path
    Optional Arguments :
        -Shallow [default value 'False']
                Shallow compile
```

A Súgó adataiban:

- A **parancs a parancs nevét adja meg** .  
- A **kötelező argumentum** felsorolja az argumentumokat, amelyeket meg kell adni.  
- A nem **kötelező argumentum** a választható argumentumokat listázza alapértelmezett értékekkel.  A nem kötelező logikai argumentumok nem rendelkeznek paraméterekkel, és a megjelenésük negatív az alapértelmezett értékük.

### <a name="return-value-and-logging"></a>Visszatérési érték és naplózás

A segítő alkalmazás a **0** értéket adja vissza a sikeres művelethez, és **-1** a hiba esetén. Alapértelmezés szerint a segítő az összes üzenetet az aktuális konzolra küldi. A parancsok többsége azonban támogatja a **-MessageOut path_to_log_file** opcionális argumentumot, amely átirányítja a kimeneteket egy naplófájlba.

### <a name="environment-variable-configuring"></a>Környezeti változó konfigurálása

A U-SQL helyi futtatásához meg kell adni egy megadott adatgyökerét helyi tárolási fiókként, valamint a függőségek megadott CppSDK elérési útját. Az argumentumot a parancssorban is beállíthatja, vagy beállíthatja a környezeti változót is.

- Állítsa be a **SCOPE_CPP_SDK** környezeti változót.

  Ha beolvassa a Microsoft Visual C++ és a Windows SDK Data Lake Tools for Visual Studio telepítésével, ellenőrizze, hogy rendelkezik-e a következő mappával:

    `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`

  Adjon meg egy új környezeti változót, amelynek neve **SCOPE_CPP_SDK** , hogy erre a könyvtárra mutasson. Vagy másolja a mappát a másik helyre, és a **SCOPE_CPP_SDKként** válassza a következőt:.

  A környezeti változó beállítása mellett a **-CppSDK** argumentumot is megadhatja a parancssor használatakor. Ez az argumentum felülírja az alapértelmezett CppSDK környezeti változót.

- Állítsa be a **LOCALRUN_DATAROOT** környezeti változót.

  Definiáljon egy új környezeti változót, amelynek neve **LOCALRUN_DATAROOT** , amely az adatgyökérre mutat.

  A környezeti változó beállítása mellett a **-DataRoot** argumentumot is megadhatja az adatgyökér elérési úttal, ha parancssort használ. Ez az argumentum felülírja az alapértelmezett adatgyökér környezeti változót. Ezt az argumentumot minden futtatott parancssorhoz hozzá kell adnia, hogy felülírja az alapértelmezett adatgyökér környezeti változót az összes művelethez.

### <a name="sdk-command-line-usage-samples"></a>SDK parancssori használati minták

#### <a name="compile-and-run"></a>Fordítás és Futtatás

A **Run** parancs a parancsfájl fordítására, majd lefordított eredményeinek végrehajtására szolgál. A parancssori argumentumok kombinációja a **fordítás** és a **végrehajtás** során.

```console
LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]
```

A **Futtatás** nem kötelező argumentumai a következők:

|Argumentum|Alapértelmezett érték|Leírás|
|--------|-------------|-----------|
|– CodeBehind|Hamis|A szkript a. CS kód mögött található.|
|-CppSDK| |CppSDK könyvtár|
|– DataRoot| DataRoot környezeti változó|Helyi Futtatás DataRoot, alapértelmezett érték: "LOCALRUN_DATAROOT" környezeti változó|
|-MessageOut| |Konzolon lévő üzenetek kiírása fájlba|
|– Párhuzamos|1|A terv futtatása a megadott párhuzamosságtal|
|– Referenciák| |Azon elérési utak listája, amelyekhez további hivatkozásokat vagy a kód mögött található, ";" karakterrel elválasztott adatfájlok találhatók.|
|-UdoRedirect|Hamis|Udo Assembly-átirányítás konfigurációjának előállítása|
|-UseDatabase|master|Az ideiglenes szerelvény-regisztráció mögötti kódokhoz használandó adatbázis|
|-Részletes|Hamis|Részletes kimenetek megjelenítése futtatókörnyezetből|
|-WorkDir|Aktuális könyvtár|A fordítóprogram használatának és kimenetének könyvtára|
|-RunScopeCEP|0|A használandó ScopeCEP mód|
|-ScopeCEPTempPath|temp|Az adatfolyam-továbbításhoz használandó ideiglenes útvonal|
|-OptFlags| |Az optimalizáló jelzők vesszővel tagolt listája|

Bemutatunk egy példát:

`LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose`

A **fordítás** és a **végrehajtás** kombinációja mellett külön is lefordíthatja és végrehajthatja a lefordított végrehajtható fájlokat.

#### <a name="compile-a-u-sql-script"></a>U-SQL-szkript fordítása

A **fordítási** paranccsal egy U-SQL-szkriptet állíthat össze a végrehajtható fájlok számára.

```console
LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]
```

A **fordítás** nem kötelező argumentumai a következők:

|Argumentum|Leírás|
|--------|-----------|
| -CodeBehind [alapértelmezett érték: "false"]|A szkript a. CS kód mögött található.|
| -CppSDK [alapértelmezett érték: ""]|CppSDK könyvtár|
| -DataRoot [alapértelmezett érték: "DataRoot környezeti változó"]|Helyi Futtatás DataRoot, alapértelmezett érték: "LOCALRUN_DATAROOT" környezeti változó|
| -MessageOut [alapértelmezett érték: ""]|Konzolon lévő üzenetek kiírása fájlba|
| -Hivatkozások [alapértelmezett érték: ""]|Azon elérési utak listája, amelyekhez további hivatkozásokat vagy a kód mögött található, ";" karakterrel elválasztott adatfájlok találhatók.|
| -Sekély [alapértelmezett érték: "false"]|Sekély fordítás|
| -UdoRedirect [alapértelmezett érték: "false"]|Udo Assembly-átirányítás konfigurációjának előállítása|
| -UseDatabase [alapértelmezett érték: "Master"]|Az ideiglenes szerelvény-regisztráció mögötti kódokhoz használandó adatbázis|
| -WorkDir [alapértelmezett érték: "aktuális könyvtár"]|A fordítóprogram használatának és kimenetének könyvtára|
| -RunScopeCEP [alapértelmezett érték: "0"]|A használandó ScopeCEP mód|
| -ScopeCEPTempPath [alapértelmezett érték: "Temp"]|Az adatfolyam-továbbításhoz használandó ideiglenes útvonal|
| -OptFlags [alapértelmezett érték: ""]|Az optimalizáló jelzők vesszővel tagolt listája|

Íme néhány felhasználási példa.

Egy U-SQL-szkript fordítása:

```console
LocalRunHelper compile -Script d:\test\test1.usql
```

Állítson össze egy U-SQL-parancsfájlt, és állítsa be az adatgyökér mappát. Vegye figyelembe, hogy ez felülírja a Set környezeti változót.

```console
LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot
```

Állítson össze egy U-SQL-parancsfájlt, és állítson be egy munkakönyvtárat, egy hivatkozási szerelvényt és egy adatbázist:

```console
LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB
```

#### <a name="execute-compiled-results"></a>Lefordított eredmények végrehajtása

A **végrehajtás** parancs a lefordított eredmények végrehajtásához használatos.

```console
LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]
```

A **végrehajtás** nem kötelező argumentumai a következők:

|Argumentum|Alapértelmezett érték|Leírás|
|--------|-------------|-----------|
|– DataRoot | '' |A metaadatok végrehajtásához szükséges adatok gyökerét. Alapértelmezés szerint a **LOCALRUN_DATAROOT** környezeti változót.|
|-MessageOut | '' |Üzenetek kiírása a konzolon egy fájlba.|
|– Párhuzamos | 1 |A generált helyi Futtatás lépéseinek a megadott párhuzamossági szinttel való futtatásának jelzője.|
|-Részletes | Hamis |A futtatókörnyezet részletes kimeneteit jeleníti meg.|

Példa a használati példákra:

```console
LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5
```

## <a name="use-the-sdk-with-programming-interfaces"></a>Az SDK használata programozási felületek használatával

A programozási felületek mind a LocalRunHelper.exe találhatók. A u-SQL SDK és a C# tesztelési keretrendszer funkcióinak integrálásához használhatja a U-SQL-parancsfájlok helyi tesztelését. Ebben a cikkben a Standard C# Unit test Project használatával mutatjuk be, hogyan használhatók ezek a felületek a U-SQL-szkriptek teszteléséhez.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>1. lépés: C# egység tesztelési projekt és konfiguráció létrehozása

- Hozzon létre egy C# egységnyi tesztelési projektet a fájl > új > Project > Visual C# > test > Unit test Project használatával.
- LocalRunHelper.exe hozzáadása hivatkozásként a projekthez. A LocalRunHelper.exe a Nuget csomagban \build\runtime\LocalRunHelper.exe található.

   ![Azure Data Lake U-SQL SDK-hozzáadási hivatkozás](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- Az U-SQL SDK **csak** az x64-es környezetet támogatja, ügyeljen arra, hogy x64-ként állítsa be a Build-platform célját. Beállíthatja, hogy a Project tulajdonságon keresztül > Build > platform célját.

   ![A U-SQL SDK Azure Data Lake az x64-projekt konfigurálása](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Győződjön meg arról, hogy a tesztkörnyezet x64-ként van beállítva. A Visual Studióban beállíthatja a teszt > tesztelési beállítások > az alapértelmezett processzor-architektúra > x64.

   ![A U-SQL SDK Azure Data Lake az x64-tesztkörnyezet konfigurálása](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Ügyeljen arra, hogy az összes függőségi fájlt a NugetPackage\build\runtime\ alá másolja a projekt munkakönyvtárra, amely általában a ProjectFolder\bin\x64\Debug. alatt van

### <a name="step-2-create-u-sql-script-test-case"></a>2. lépés: a U-SQL-parancsfájl tesztelési esetének létrehozása

Alább látható a U-SQL-szkriptek tesztelésének mintája. Teszteléshez a parancsfájlokat, a bemeneti fájlokat és a várt kimeneti fájlokat kell előkészítenie.

```usql
using System;
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.IO;
using System.Text;
using System.Security.Cryptography;
using Microsoft.Analytics.LocalRun;
namespace UnitTestProject1
{
    [TestClass]
    public class USQLUnitTest
    {
        [TestMethod]
        public void TestUSQLScript()
        {
            //Specify the local run message output path
            StreamWriter MessageOutput = new StreamWriter("../../../log.txt");
            LocalRunHelper localrun = new LocalRunHelper(MessageOutput);
            //Configure the DateRoot path, Script Path and CPPSDK path
            localrun.DataRoot = "../../../";
            localrun.ScriptPath = "../../../Script/Script.usql";
            localrun.CppSdkDir = "../../../CppSDK";
            //Run U-SQL script
            localrun.DoRun();
            //Script output
            string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");
            //Expected script output
            string ExpectedResult = "../../../ExpectedOutput/result.csv";
            Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);
            //Don't forget to close MessageOutput to get logs into file
            MessageOutput.Close();
        }
    }
}
namespace Test.Helpers
{
    public static class FileAssert
    {
        static string GetFileHash(string filename)
        {
            Assert.IsTrue(File.Exists(filename));
            using (var hash = new SHA1Managed())
            {
                var clearBytes = File.ReadAllBytes(filename);
                var hashedBytes = hash.ComputeHash(clearBytes);
                return ConvertBytesToHex(hashedBytes);
            }
        }
        static string ConvertBytesToHex(byte[] bytes)
        {
            var sb = new StringBuilder();
            for (var i = 0; i < bytes.Length; i++)
            {
                sb.Append(bytes[i].ToString("x"));
            }
            return sb.ToString();
        }
        public static void AreEqual(string filename1, string filename2)
        {
            string hash1 = GetFileHash(filename1);
            string hash2 = GetFileHash(filename2);
            Assert.AreEqual(hash1, hash2);
        }
    }
}
```

### <a name="programming-interfaces-in-localrunhelperexe"></a>Programozási felületek LocalRunHelper.exe

A LocalRunHelper.exe az U-SQL helyi fordításához, futtatásához stb. programozási felületeket biztosít. A felületek a következő módon vannak felsorolva.

### <a name="constructor"></a>Konstruktor

nyilvános LocalRunHelper ([System. IO. TextWriter messageOutput = null])

|Paraméter|Típus|Leírás|
|---------|----|-----------|
|messageOutput|System. IO. TextWriter|a kimeneti üzenetek esetében állítsa a null értéket a konzol használatához|

### <a name="properties"></a>Tulajdonságok

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|AlgebraPath|sztring|Az algebra-fájl elérési útja (az algebra-fájl az egyik fordítási eredmény)|
|CodeBehindReferences|sztring|Ha a parancsfájl további kódokat tartalmaz a hivatkozások mögött, adja meg a ";" karakterrel elválasztott elérési utakat.|
|CppSdkDir|sztring|CppSDK könyvtár|
|CurrentDir|sztring|Aktuális könyvtár|
|DataRoot|sztring|Az adatgyökér elérési útja|
|DebuggerMailPath|sztring|A hibakereső mailslot elérési útja|
|GenerateUdoRedirect|logikai|Ha a szerelvény-betöltési átirányítás felülbírálását kívánja előállítani|
|HasCodeBehind|logikai|Ha a parancsfájl mögött van kód|
|InputDir|sztring|A bemeneti adatok könyvtára|
|MessagePath|sztring|Üzenet kiírási fájljának elérési útja|
|OutputDir|sztring|A kimeneti adatokat tároló könyvtár|
|Párhuzamosság|int|Párhuzamosság az algebra futtatásához|
|ParentPid|int|Annak a szülőnek a PID azonosítója, amelyen a szolgáltatás ki van kapcsolva, 0 vagy negatív értékre kell állítani|
|ResultPath|sztring|Eredmény memóriaképének elérési útja|
|RuntimeDir|sztring|Futtatókörnyezet könyvtára|
|ScriptPath|sztring|Hol található a parancsfájl|
|Sekély|logikai|Sekély fordítás vagy nem|
|TempDir|sztring|Ideiglenes könyvtár|
|UseDataBase|sztring|Az ideiglenes szerelvény-regisztráció mögötti kódokhoz használandó adatbázis megadása, alapértelmezés szerint a főkiszolgáló|
|WorkDir|sztring|Előnyben részesített munkakönyvtár|

### <a name="method"></a>Metódus

|Metódus|Leírás|Visszatérési|Paraméter|
|------|-----------|------|---------|
|nyilvános Boole DoCompile ()|Az U-SQL-szkript fordítása|Igaz siker esetén| |
|nyilvános Boole DoExec ()|A lefordított eredmény végrehajtása|Igaz siker esetén| |
|nyilvános Boole DoRun ()|Az U-SQL-szkript futtatása (fordítás + végrehajtás)|Igaz siker esetén| |
|nyilvános Boole IsValidRuntimeDir (karakterlánc elérési útja)|Ellenőrizze, hogy a megadott elérési út érvényes futtatókörnyezet-elérési út-e|Igaz érvényes|A futásidejű könyvtár elérési útja|

## <a name="faq-about-common-issue"></a>Gyakori problémák

### <a name="error-1"></a>1. hiba

E_CSC_SYSTEM_INTERNAL: belső hiba! Nem tölthető be a (z) "ScopeEngineManaged.dll" fájl vagy szerelvény vagy annak valamelyik függősége. A megadott modul nem található.

Ellenőrizze a következőt:

- Győződjön meg arról, hogy rendelkezik x64-környezettel. A Build cél platformnak és a tesztkörnyezetben x64-nek kell lennie, az **1. lépés: C# egység tesztelési projekt és a fenti konfiguráció létrehozása című témakörben** talál.
- Győződjön meg arról, hogy az összes függőségi fájlt átmásolta a NugetPackage\build\runtime\ a Project Working könyvtárba.

## <a name="next-steps"></a>Következő lépések

- A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
- A diagnosztikai információk naplózásához tekintse meg [a Azure Data Lake Analytics diagnosztikai naplóinak elérését](data-lake-analytics-diagnostic-logs.md)ismertető témakört.
- Összetettebb lekérdezés megjelenítéséhez tekintse meg a [webhelyek naplófájljainak elemzése Azure Data Lake Analytics használatával](data-lake-analytics-analyze-weblogs.md)című témakört.
- A feladat részleteinek megtekintéséhez lásd: [Azure Data Lake Analytics feladatok böngésző és feladat nézetének használata](data-lake-analytics-data-lake-tools-view-jobs.md).
- A csúcspont-végrehajtási nézet használatához lásd: [a Data Lake Tools for Visual Studio csúcs-végrehajtási nézetének használata](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
