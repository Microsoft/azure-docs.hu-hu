---
title: 'Oktatóanyag: fordítási alkalmazás létrehozása WPF, C#-Translator használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy WPF-alkalmazást hoz létre a szöveg fordításához, a nyelvfelismerés és a helyesírás-ellenőrzés végrehajtásához egyetlen előfizetési kulccsal.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 05/26/2020
ms.author: lajanuar
ms.custom: devx-track-csharp
ms.openlocfilehash: dd5b83908ae7c6d62acd3391933028685facf755
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927474"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>Oktatóanyag: fordítási alkalmazás létrehozása WPF-mel

Ebben az oktatóanyagban egy olyan [Windows megjelenítési alaprendszer (WPF)](/visualstudio/designers/getting-started-with-wpf) alkalmazást fog létrehozni, amely az Azure Cognitive Servicest használja a szöveges fordításhoz, a nyelvfelismerés és a helyesírás-ellenőrzéshez egyetlen előfizetési kulccsal. Az alkalmazás az API-kat a fordítótól és a [Bing spell Checktól](https://azure.microsoft.com/services/cognitive-services/spell-check/)hívja meg.

Mi az a WPF? Ez egy felhasználói felületi keretrendszer, amely asztali ügyfélprogramokat hoz létre. A WPF fejlesztői platform számos alkalmazás-fejlesztési funkciót támogat, többek között az alkalmazás-modellt, az erőforrásokat, a vezérlőket, a grafikát, az elrendezést, az adatkötéseket, a dokumentumokat és a biztonságot. Ez a .NET-keretrendszer egy részhalmaza, így ha korábban a ASP.NET vagy a Windows Forms használatával már létrehozott alkalmazásokat a .NET-keretrendszerrel, a programozási élménynek ismerősnek kell lennie. A WPF az Extensible app Markup Language (XAML) használatával biztosít egy deklaratív modellt az alkalmazások programozásához, amelyet a következő szakaszokban fogunk áttekinteni.

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * WPF-projekt létrehozása a Visual Studióban
> * Szerelvények és NuGet-csomagok hozzáadása a projekthez
> * Az alkalmazás felhasználói felületének létrehozása a XAML
> * A fordító használata nyelvek beszerzéséhez, szöveg fordításához és a forrás nyelvének észleléséhez
> * A Bing Spell Check API használata a bevitel ellenőrzéséhez és a fordítás pontosságának javításához
> * A WPF-alkalmazás futtatása

### <a name="cognitive-services-used-in-this-tutorial"></a>Az oktatóanyagban használt Cognitive Services

Ez a lista tartalmazza az oktatóanyagban használt Cognitive Services. Az egyes szolgáltatásokhoz tartozó API-referenciák tallózásához kövesse a hivatkozást.

| Szolgáltatás | Szolgáltatás | Leírás |
|---------|---------|-------------|
| Translator | [Nyelvek beolvasása](./reference/v3-0-languages.md) | A szöveges fordításhoz támogatott nyelvek teljes listájának beolvasása. |
| Translator | [Fordítása](./reference/v3-0-translate.md) | Szöveg fordítása több mint 70 nyelvre. |
| Translator | [Kinyomoz](./reference/v3-0-detect.md) | A bemeneti szöveg nyelvének észlelése. Az észlelés megbízhatósági pontszámát tartalmazza. |
| Bing – Helyesírás-ellenőrzés | [Spell Check](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | A fordítási pontosság javítása érdekében javítsa a helyesírási hibákat. |

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt a következőkre lesz szüksége:

* Azure Cognitive Services-előfizetés. [Cognitive Services kulcs beolvasása](../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).
* Windows rendszerű gép
* [Visual Studio 2019](https://www.visualstudio.com/downloads/) – Közösség vagy vállalat

> [!NOTE]
> Javasoljuk, hogy az oktatóanyaghoz az USA nyugati régiójában hozzon létre előfizetést. Ellenkező esetben módosítania kell a kódban lévő végpontokat és régiókat, ahogy ezt a gyakorlatot elvégezte.  

## <a name="create-a-wpf-app-in-visual-studio"></a>WPF-alkalmazás létrehozása a Visual Studióban

Első lépésként be kell állítania a projektjét a Visual Studióban.

1. Nyissa meg a Visual Studiót. Válassza **az új projekt létrehozása** lehetőséget.
1. A **create a New Project (új projekt létrehozása**) területen keresse meg és válassza ki a **WPF-alkalmazást (.NET-keretrendszer)**. Kiválaszthatja a C# **nyelvről** lehetőséget, és szűkítheti a beállításokat.
1. Válassza a **tovább** lehetőséget, majd nevezze el a projektet `MSTranslatorDemo` .
1. Állítsa a keretrendszer verzióját a **.NET-keretrendszer 4.7.2** vagy újabb verzióra, majd válassza a **Létrehozás** lehetőséget.
   ![Adja meg a név és a keretrendszer verzióját a Visual Studióban](media/name-wpf-project-visual-studio.png)

A projekt létrejött. Megfigyelheti, hogy két lap van megnyitva: `MainWindow.xaml` és `MainWindow.xaml.cs` . Ebben az oktatóanyagban programkódot adunk hozzá a két fájlhoz. Módosítjuk `MainWindow.xaml` az alkalmazás felhasználói felületét. A `MainWindow.xaml.cs` fordítói és Bing spell Checki hívások esetében módosítjuk.
   ![A környezet áttekintése](media/blank-wpf-project.png)

A következő szakaszban szerelvényeket és NuGet-csomagokat fogunk hozzáadni a projekthez további funkciók, például a JSON-elemzés érdekében.

## <a name="add-references-and-nuget-packages-to-your-project"></a>Referenciák és NuGet-csomagok hozzáadása a projekthez

A projekthez szükség van a .NET-keretrendszer néhány szerelvényére és NewtonSoft.Jsre, amelyeket a NuGet csomagkezelő használatával fogunk telepíteni.

### <a name="add-net-framework-assemblies"></a>.NET-keretrendszer szerelvények hozzáadása

Vegyünk fel szerelvényeket a projektbe objektumok szerializálásához és deszerializálásához, valamint a HTTP-kérések és válaszok kezeléséhez.

1. Keresse meg a projektet a Visual Studio Megoldáskezelőban. Kattintson a jobb gombbal a projektre, majd válassza a **> hivatkozás hozzáadása** lehetőséget, amely megnyitja a **Reference Managert**.
1. A **szerelvények** lapon az összes rendelkezésre álló .NET-keretrendszer-szerelvény szerepel. A hivatkozások kereséséhez használja a jobb felső sarokban található keresési sávot.
   ![Szerelvény-hivatkozások hozzáadása](media/add-assemblies-2019.png)
1. Válassza ki az alábbi hivatkozásokat a projekthez:
   * [System.Runtime.Serialization](/dotnet/api/system.runtime.serialization)
   * [System.Web](/dotnet/api/system.web)
   * System.Web.Extensions
   * [System. Windows](/dotnet/api/system.windows)
1. Miután hozzáadta ezeket a hivatkozásokat a projekthez, a **Reference Manager** bezárásához kattintson **az OK** gombra.

> [!NOTE]
> Ha többet szeretne megtudni a szerelvények hivatkozásairól, tekintse meg a következő témakört [: útmutató: hivatkozás hozzáadása vagy eltávolítása a Reference Manager használatával](/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager).

### <a name="install-newtonsoftjson"></a>NewtonSoft.Jstelepítése

Az alkalmazás a NewtonSoft.Json használatával deszerializálja a JSON-objektumokat. A csomag telepítéséhez kövesse az alábbi utasításokat.

1. Keresse meg a projektet a Visual Studio Megoldáskezelőban, és kattintson a jobb gombbal a projektre. Válassza a **NuGet-csomagok kezelése** lehetőséget.
1. Keresse meg és válassza ki a **Tallózás** lapot.
1. Adja meg [NewtonSoft.Js](https://www.nuget.org/packages/Newtonsoft.Json/) be a keresési sávon.

    ![NewtonSoft.Jsmegkeresése és telepítése](media/nuget-package-manager.png)

1. Válassza ki a csomagot, majd kattintson a **telepítés** gombra.
1. A telepítés befejezésekor kattintson a lap bezárásához.

## <a name="create-a-wpf-form-using-xaml"></a>WPF-űrlap létrehozása a XAML használatával

Az alkalmazás használatához felhasználói felületre lesz szüksége. A XAML használatával létrehozunk egy űrlapot, amely lehetővé teszi a felhasználók számára a bemeneti és a fordítási nyelvek kijelölését, a fordítás szövegének megadását és a fordítási kimenet megjelenítését.

Nézzük meg, hogy mi építünk.

![WPF XAML felhasználói felület](media/translator-text-csharp-xaml.png)

A felhasználói felület a következő összetevőket tartalmazza:

| Név | Típus | Description |
|------|------|-------------|
| `FromLanguageComboBox` | ComboBox | Megjeleníti a Microsoft Translator által a szöveges fordításhoz támogatott nyelvek listáját. A felhasználó kiválasztja azt a nyelvet, amelyről a fordítás történik. |
| `ToLanguageComboBox` | ComboBox | Ugyanazokat a nyelveket jeleníti meg, mint a `FromComboBox` , de azon nyelv kiválasztására szolgál, amelyet a felhasználó lefordít. |
| `TextToTranslate` | TextBox | Lehetővé teszi a felhasználó számára a fordítás szövegének megadását. |
| `TranslateButton` | Gomb | Ezzel a gombbal lefordíthatja a szöveget. |
| `TranslatedTextLabel` | Címke | Megjeleníti a fordítást. |
| `DetectedLanguageLabel` | Címke | Megjeleníti a lefordítani kívánt szöveg észlelt nyelvét ( `TextToTranslate` ). |

> [!NOTE]
> Ezt az űrlapot a XAML forráskódjának használatával hozunk létre, azonban létrehozhatja az űrlapot a Visual Studióban található szerkesztővel.

Adjuk hozzá a kódot a projekthez.

1. A Visual Studióban válassza a fület `MainWindow.xaml` .
1. Másolja ezt a kódot a projektbe, majd válassza a **fájl > mentse a MainWindow. XAML fájlt** a módosítások mentéséhez.
   ```xaml
   <Window x:Class="MSTranslatorDemo.MainWindow"
           xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
           xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
           xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
           xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
           xmlns:local="clr-namespace:MSTranslatorDemo"
           mc:Ignorable="d"
           Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
       <Grid>
           <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
           <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
           <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
           <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

           <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
           <ComboBox x:Name="ToLanguageComboBox"
                   HorizontalAlignment="Left"
                   Margin="306,88,0,0"
                   VerticalAlignment="Top"
                   Width="175" FontSize="14" TabIndex="2">

           </ComboBox>
           <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
           <ComboBox x:Name="FromLanguageComboBox"
               HorizontalAlignment="Left"
               Margin="42,88,0,0"
               VerticalAlignment="Top"
               Width="175" FontSize="14" TabIndex="1"/>
           <Label x:Name="TranslatedTextLabel" Content="Translation is displayed here." HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
           <Label x:Name="DetectedLanguageLabel" Content="Autodetected language is displayed here." HorizontalAlignment="Left" Margin="39,288,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="84" BorderThickness="0"/>
       </Grid>
   </Window>
   ```
Ekkor látnia kell az alkalmazás felhasználói felületének előzetesét a Visual Studióban. A fenti képhez hasonlóan kell kinéznie.

Ennyi, az űrlap készen áll. Most írj egy kódot a szöveges fordítás és a Bing Spell Check használatához.

> [!NOTE]
> Nyugodtan megteheti ezt az űrlapot, vagy létrehozhatja a sajátját.

## <a name="create-your-app"></a>Az alkalmazás létrehozása

`MainWindow.xaml.cs` az alkalmazást vezérlő kódot tartalmazza. A következő néhány szakaszban kód hozzáadásával feltöltjük a legördülő menüket, és meghívhatjuk a fordító és Bing Spell Check által elérhetővé tett néhány API-t.

* A program indításakor és `MainWindow` példányain a `Languages` fordító metódusa hívja le és töltse ki a nyelvi kiválasztási legördülő listát. Ez az egyes munkamenetek elején történik.
* Ha a **fordítás** gombra kattint, a rendszer beolvassa a felhasználó nyelvének kijelölését és szövegét, a helyesírás-ellenőrzés a bemeneten történik, a fordítás és az észlelt nyelv pedig megjelenik a felhasználó számára.
  * A `Translate` fordító metódusának hívása a szöveg lefordítására `TextToTranslate` . Ez a hívás a `to` `from` legördülő menükben kiválasztott és választott nyelveket is tartalmazza.
  * A `Detect` fordító metódusának meg kell hívnia a szöveg nyelvét `TextToTranslate` .
  * Bing Spell Check a hibák érvényesítésére `TextToTranslate` és módosítására szolgál.

Az összes projekt be van ágyazva a `MainWindow : Window` osztályba. Kezdjük azzal, hogy hozzáadunk egy kódot az előfizetési kulcs beállításához, deklaráljuk a fordítói és Bing Spell Checki végpontokat, és inicializáljuk az alkalmazást.

1. A Visual Studióban válassza a fület `MainWindow.xaml.cs` .
1. Cserélje le az előre megadott `using` utasításokat a következőre.  
   ```csharp
   using System;
   using System.Windows;
   using System.Net;
   using System.Net.Http;
   using System.IO;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using Newtonsoft.Json;
   ```
1. Keresse meg az `MainWindow : Window` osztályt, és cserélje le a következő kódra:
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
       const string BING_SPELL_CHECK_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/bing/v7.0/spellcheck/";
       // An array of language codes
       private string[] languageCodes;

       // Dictionary to map language codes from friendly name (sorted case-insensitively on language name)
       private SortedDictionary<string, string> languageCodesAndTitles =
           new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

       // Global exception handler to display error message and exit
       private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
       {
           Exception e = (Exception)args.ExceptionObject;
           MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
           System.Windows.Application.Current.Shutdown();
       }
       // MainWindow constructor
       public MainWindow()
       {
           // Display a message if unexpected error is encountered
           AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

           if (COGNITIVE_SERVICES_KEY.Length != 32)
           {
               MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                   "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                   "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
               System.Windows.Application.Current.Shutdown();
           }
           else
           {
               // Start GUI
               InitializeComponent();
               // Get languages for drop-downs
               GetLanguagesForTranslate();
               // Populate drop-downs with values from GetLanguagesForTranslate
               PopulateLanguageMenus();
           }
       }
   // NOTE:
   // In the following sections, we'll add code below this.
   }
   ```
1. Adja hozzá Cognitive Services előfizetési kulcsát, és mentse.

Ebben a kódrészletben két, a fordításhoz elérhető nyelvekkel kapcsolatos információt tartalmazó tag-változót jelentettünk be:

| Változó | Típus | Description |
|----------|------|-------------|
|`languageCodes` | Karakterláncok tömbje |A nyelvkódokat gyorsítótárazza. A Translator szolgáltatás rövid kódokat használ a nyelvek azonosítására (például: `en` = angol). |
|`languageCodesAndTitles` | Rendezett szótár | A felhasználói felületen megjelenő „felhasználóbarát” neveket képezi le az API által használt rövid kódokra. Az elemeket betűrendbe rendezi, és nem veszi figyelembe a nagy- és kisbetűket. |

Ezután a `MainWindow` konstruktoron belül a következővel bővült a hibakezelés: `HandleExceptions` . Ez a hibakezelés biztosítja, hogy a rendszer riasztást biztosítson, ha nem kezelik a kivételeket. Ezután ellenőrizze, hogy a megadott előfizetési kulcs 32 karakter hosszúságú-e. Hiba történik, ha a kulcs kisebb, mint 32 karakternél.

Ha vannak olyan kulcsok, amelyeknek legalább a megfelelő hosszúsága van, a `InitializeComponent()` hívás a fő alkalmazás ablakának XAML-leírásának megkeresésével, betöltésével és másolásával válik elérhetővé a felhasználói felületen.

Végül hozzáadunk egy kódot a metódusok meghívásához a fordítási nyelvek lekéréséhez és az alkalmazás felhasználói felületének legördülő menüinek feltöltéséhez. Ne aggódjon, hamarosan megkezdjük a hívások mögötti kódot.

## <a name="get-supported-languages"></a>Támogatott nyelvek lekérése

A Translator jelenleg több mint 70 nyelvet támogat. Mivel az új nyelvi támogatást az idő múlásával vesszük fel, javasoljuk, hogy a fordító által közzétett nyelvi erőforrást ne rögzítjük az alkalmazásban.

Ebben a szakaszban egy `GET` , a Languages (nyelvek) erőforrásra vonatkozó kérelmet hozunk létre, amely azt határozza meg, hogy szeretnénk-e a fordításhoz elérhető nyelvek listáját használni.

> [!NOTE]
> A nyelvek erőforrás lehetővé teszi a nyelvi támogatás szűrését a következő lekérdezési paraméterekkel: az írás, a szótár és a fordítás. További információ: API- [referenciák](./reference/v3-0-languages.md).

Mielőtt továbblépne, vessünk egy pillantást a minta kimenetre a languages erőforrás meghívásához:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    }
    // Additional languages are provided in the full JSON output.
}
```

Ebből a kimenetből kinyerhetjük a nyelvi kódot és az `name` adott nyelvet. Az alkalmazás a NewtonSoft.Json használatával deszerializálja a JSON-objektumot ( [`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm) ).

Vegyen fel egy metódust, ahol az utolsó szakaszban leálltunk, és hozzunk létre egy módszert az alkalmazásban támogatott nyelvek beszerzéséhez.

1. A Visual Studióban nyissa meg a fület `MainWindow.xaml.cs` .
2. Adja hozzá ezt a kódot a projekthez:
   ```csharp
   // ***** GET TRANSLATABLE LANGUAGE CODES
   private void GetLanguagesForTranslate()
   {
       // Send request to get supported language codes
       string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
       WebRequest WebRequest = WebRequest.Create(uri);
       WebRequest.Headers.Add("Accept-Language", "en");
       WebResponse response = null;
       // Read and parse the JSON response
       response = WebRequest.GetResponse();
       using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
       {
           var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
           var languages = result["translation"];

           languageCodes = languages.Keys.ToArray();
           foreach (var kv in languages)
           {
               languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
           }
       }
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

A `GetLanguagesForTranslate()` metódus létrehoz egy HTTP Get kérelmet, és a `scope=translation` lekérdezési karakterlánc paraméter használatával korlátozza a kérelem hatókörét a fordításhoz támogatott nyelvekre. Az `en` értékű `Accept-Language` fejléc hozzáadásával a támogatott nyelvek neve angolul jelenik meg.

A JSON-válasz elemzése és átalakítása szótárba. Ezután hozzáadja a nyelvi kódokat a `languageCodes` tag változóhoz. A nyelvi kódokat és a nyelvek felhasználóbarát nevét tartalmazó kulcs-érték-párokból az alkalmazás egy hurkot hoz létre, majd hozzáadja azokat a `languageCodesAndTitles` tagváltozóhoz. Az űrlap legördülő menüje megjeleníti a felhasználóbarát neveket, de a fordítás igényléséhez kódokat kell kérni.

## <a name="populate-language-drop-down-menus"></a>Nyelvi legördülő menük feltöltése

A felhasználói felület a XAML használatával van definiálva, így nem kell sokat beállítania a hívás mellett `InitializeComponent()` . Ehhez hozzá kell adnia a felhasználóbarát nyelvi neveket a **fordításhoz** , és le **kell fordítani** a legördülő menüket. A `PopulateLanguageMenus()` metódus hozzáadja a neveket.

1. A Visual Studióban nyissa meg a fület `MainWindow.xaml.cs` .
2. Adja hozzá ezt a kódot a projekthez a `GetLanguagesForTranslate()` metódus alatt:
   ```csharp
   private void PopulateLanguageMenus()
   {
       // Add option to automatically detect the source language
       FromLanguageComboBox.Items.Add("Detect");

       int count = languageCodesAndTitles.Count;
       foreach (string menuItem in languageCodesAndTitles.Keys)
       {
           FromLanguageComboBox.Items.Add(menuItem);
           ToLanguageComboBox.Items.Add(menuItem);
       }

       // Set default languages
       FromLanguageComboBox.SelectedItem = "Detect";
       ToLanguageComboBox.SelectedItem = "English";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Ez a metódus megismétli a `languageCodesAndTitles` szótárt, és hozzáadja az egyes kulcsokat mindkét menühöz. A menük kitöltése után a és a nyelvre való alapértelmezett érték az **észlelés** és az **angol** .

> [!TIP]
> Ha a menüknek nincs alapértelmezett értéke, a felhasználó a forrásnyelv és a célnyelv kiválasztása nélkül nem kattinthat a **Translate** (Fordítás) gombra. Ha vannak alapértelmezett beállítások, a felhasználónak nem kell ezzel törődnie.

Most, hogy már `MainWindow` inicializálva lett, és a felhasználói felület létrejött, ez a kód addig nem fog futni, amíg a **fordítás**  gombra nem kattint.

## <a name="detect-language-of-source-text"></a>Forrás szöveg nyelvének észlelése

Most létre fogjuk hozni a módszert a forrás szöveg nyelvének észleléséhez (a szöveg területéhez beírt szöveg) a fordító használatával. A kérelem által visszaadott értéket később a fordítási kérelemben fogjuk használni.

1. A Visual Studióban nyissa meg a fület `MainWindow.xaml.cs` .
2. Adja hozzá ezt a kódot a projekthez a `PopulateLanguageMenus()` metódus alatt:
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "application/json; charset=utf-8";
       detectLanguageWebRequest.Method = "POST";

       // Send request
       var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
       string jsonText = serializer.Serialize(text);

       string body = "[{ \"Text\": " + jsonText + " }]";
       byte[] data = Encoding.UTF8.GetBytes(body);

       detectLanguageWebRequest.ContentLength = data.Length;

       using (var requestStream = detectLanguageWebRequest.GetRequestStream())
           requestStream.Write(data, 0, data.Length);

       HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

       // Read and parse JSON response
       var responseStream = response.GetResponseStream();
       var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
       dynamic jsonResponse = serializer.DeserializeObject(jsonString);

       // Fish out the detected language code
       var languageInfo = jsonResponse[0];
       if (languageInfo["score"] > (decimal)0.5)
       {
           DetectedLanguageLabel.Content = languageInfo["language"];
           return languageInfo["language"];
       }
       else
           return "Unable to confidently detect input language.";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Ez a metódus egy HTTP `POST` -kérelmet hoz létre az észlelési erőforráshoz. Egyetlen argumentumot vesz igénybe, `text` amely a kérelem törzsének megfelelően lesz átadva. Később, amikor létrehozjuk a fordítási kérést, a felhasználói felületen megadott szöveg át lesz adva a nyelvi észleléshez.

Ez a módszer emellett kiértékeli a válasz megbízhatósági pontszámát. Ha a pontszám nagyobb, mint `0.5` , akkor az észlelt nyelv megjelenik a felhasználói felületen.

## <a name="spell-check-the-source-text"></a>Helyesírás-ellenőrzés a forrás szövegében

Most létrehozunk egy módszert a forrás szövegének helyesírás-ellenőrzéséhez a Bing Spell Check API használatával. A helyesírás-ellenőrzés biztosítja, hogy a fordítóból pontos fordítást kapjon. A rendszer a fordítási kérés során a forrás szövegének minden helyesbítését átadja **a** fordítási kérelemben.

1. A Visual Studióban nyissa meg a fület `MainWindow.xaml.cs` .
2. Adja hozzá ezt a kódot a projekthez a `DetectLanguage()` metódus alatt:

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

    // Create and send the request
    string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
    byte[] data = Encoding.UTF8.GetBytes(body);
    spellCheckWebRequest.ContentLength = data.Length;
    using (var requestStream = spellCheckWebRequest.GetRequestStream())
        requestStream.Write(data, 0, data.Length);
    HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

    // Read and parse the JSON response; get spelling corrections
    var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
    var responseStream = response.GetResponseStream();
    var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
    dynamic jsonResponse = serializer.DeserializeObject(jsonString);
    var flaggedTokens = jsonResponse["flaggedTokens"];

    // Construct sorted dictionary of corrections in reverse order (right to left)
    // This ensures that changes don't impact later indexes
    var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
    for (int i = 0; i < flaggedTokens.Length; i++)
    {
        var correction = flaggedTokens[i];
        var suggestion = correction["suggestions"][0];  // Consider only first suggestion
        if (suggestion["score"] > (decimal)0.7)         // Take it only if highly confident
            corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
    }

    // Apply spelling corrections, in order, from right to left
    foreach (int i in corrections.Keys)
    {
        var oldtext = corrections[i][0];
        var newtext = corrections[i][1];

        // Apply capitalization from original text to correction - all caps or initial caps
        if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
        else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

        text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
    }
    return text;
}
// NOTE:
// In the following sections, we'll add code below this.
```

## <a name="translate-text-on-click"></a>Szöveg lefordítása kattintáskor

Az utolsó lépés az, hogy létre kell hozni egy metódust, amely akkor lép fel, amikor a felhasználói felület **fordítás** gombjára kattint.

1. A Visual Studióban nyissa meg a fület `MainWindow.xaml.cs` .
1. Adja hozzá ezt a kódot a projekthez a `CorrectSpelling()` metódus alatt, és mentse a következőt:  
   ```csharp
   // ***** PERFORM TRANSLATION ON BUTTON CLICK
   private async void TranslateButton_Click(object sender, EventArgs e)
   {
       string textToTranslate = TextToTranslate.Text.Trim();

       string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
       string fromLanguageCode;

       // auto-detect source language if requested
       if (fromLanguage == "Detect")
       {
           fromLanguageCode = DetectLanguage(textToTranslate);
           if (!languageCodes.Contains(fromLanguageCode))
           {
               MessageBox.Show("The source language could not be detected automatically " +
                   "or is not supported for translation.", "Language detection failed",
                   MessageBoxButton.OK, MessageBoxImage.Error);
               return;
           }
       }
       else
           fromLanguageCode = languageCodesAndTitles[fromLanguage];

       string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

       // spell-check the source text if the source language is English
       if (fromLanguageCode == "en")
       {
           if (textToTranslate.StartsWith("-"))    // don't spell check in this case
               textToTranslate = textToTranslate.Substring(1);
           else
           {
               textToTranslate = CorrectSpelling(textToTranslate);
               TextToTranslate.Text = textToTranslate;     // put corrected text into input field
           }
       }
       // handle null operations: no text or same source/target languages
       if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
       {
           TranslatedTextLabel.Content = textToTranslate;
           return;
       }

       // send HTTP request to perform the translation
       string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
       string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

       System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
       var requestBody = JsonConvert.SerializeObject(body);

       using (var client = new HttpClient())
       using (var request = new HttpRequestMessage())
       {
           request.Method = HttpMethod.Post;
           request.RequestUri = new Uri(uri);
           request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
           request.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
           request.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
           request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

           var response = await client.SendAsync(request);
           var responseBody = await response.Content.ReadAsStringAsync();

           var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
           var translation = result[0]["translations"][0]["text"];

           // Update the translation field
           TranslatedTextLabel.Content = translation;
       }
   }
   ```

Az első lépés a "from" és a "to" nyelv, valamint a felhasználó által az űrlapba beírt szöveg beolvasása. Ha a forrás nyelve **észlelésre** van beállítva, a `DetectLanguage()` rendszer meghívja a forrás szöveg nyelvét. Előfordulhat, hogy a szöveg olyan nyelven van, amelyet a fordító nem támogat. Ebben az esetben egy üzenet jelenik meg, amely tájékoztatja a felhasználót, és a szöveg fordítása nélkül visszakerül.

Amennyiben a forrásnyelv az angol (akár megadja ezt, akár a rendszer maga ismeri fel), a `CorrectSpelling()` használatával ellenőrizheti, valamint szükség esetén javíthatja a helyesírást. A javított szöveg visszakerül a szövegmezőbe, így a felhasználó úgy látja, hogy helyesbítés történt.

A szöveg fordítására szolgáló kódnak ismerősnek kell lennie: hozza létre az URI-t, hozzon létre egy kérést, küldje el és elemezze a választ. A JSON-tömb egynél több objektumot is tartalmazhat a fordításhoz, azonban az alkalmazás csak egy szükséges.

Sikeres kérés után a a () `TranslatedTextLabel.Content` helyére kerül `translation` , amely frissíti a felhasználói felületet a lefordított szöveg megjelenítéséhez.

## <a name="run-your-wpf-app"></a>A WPF-alkalmazás futtatása

Ennyi a WPF használatával létrehozott, működő fordítási alkalmazás. Az alkalmazás futtatásához kattintson a **Start** gombra a Visual Studióban.

## <a name="source-code"></a>Forráskód

A projekt forráskódja elérhető a GitHubon.

* [A forráskód megismerése](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A Microsoft Translator dokumentációja](./reference/v3-0-reference.md)
