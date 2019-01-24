---
title: Smooth Streaming Windows Store-alkalmazás Oktatóanyaga |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre az Azure Media Services segítségével egy C# Windows Store alkalmazás XML prvku MediaElement vezérlővel lejátszási Smooth Stream tartalmát.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: juliako
ms.openlocfilehash: 9558a924ee151f47033178b0762a39e2d1e1f538
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828315"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Smooth Streaming-Windows Store-alkalmazás létrehozása

A Smooth Streaming Client SDK for Windows 8 lehetővé teszi a fejlesztők számára a Windows Store-alkalmazások építéséhez igény szerinti és élő Smooth Streaming-tartalmat is próbálhatja. A Smooth Streaming-tartalmat alapszintű lejátszás, az SDK-t is nyújt gazdag funkciók, például a Microsoft PlayReady-védelmet, minőségi szintű korlátozás, élő DVR-, hang stream közötti váltás, a figyelő állapotának frissítése (például a minőségi szintű módosítások), és Hiba történt eseményeket, és így tovább. A támogatott szolgáltatások további információkért lásd: a [kibocsátási megjegyzések](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). További információkért lásd: [Player keretrendszer Windows 8](http://playerframework.codeplex.com/). 

Ebben az oktatóanyagban négy leckéket tartalmazza:

1. Hozzon létre egy alapszintű zökkenőmentes Streamet biztosító Store alkalmazás
2. Adjon hozzá egy csúszka, szabályozhatja az adathordozó folyamatban
3. Jelölje be a Streamek Smooth Streaming
4. Válassza ki a Smooth Streaming nyomon követi

## <a name="prerequisites"></a>Előfeltételek
> [!NOTE]
> Windows Store projektek verzió 8.1 és korábbi verziók nem támogatottak a Visual Studio 2017-ben.  További információ: [A Visual Studio 2017 platform célcsoportkezelése és kompatibilitása](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

* Windows 8 32 bites vagy 64 bites.
* A Visual Studio-verziók 2012 2015 segítségével.
* [A Microsoft Smooth Streaming Client SDK Windows 8 rendszeren futó](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

A kész megoldás minden lecke a letölthető MSDN fejlesztői Kódminták (Kódgaléria): 

* [1. lecke](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) – egyszerű Windows 8 Smooth Streaming-Media Player esetében 
* [2. lecke](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) – egyszerű Windows 8 Smooth Streaming-Media Player a egy csúszka vezérlőt, 
* [3. lecke](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) – a Windows 8 Smooth Streaming-Stream kijelölés, a Media Player  
* [4. lecke](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) – a Windows 8 Smooth Streaming-Media Player követése kijelöléssel.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>1. lecke: Hozzon létre egy alapszintű zökkenőmentes Streamet biztosító Store alkalmazás

Ebben a leckében létrehozhat egy Windows Store-alkalmazás Smooth Stream lejátszás prvku MediaElement vezérlővel tartalom.  A futó alkalmazás hasonlóan néz ki:

![Példa az Smooth Streaming Windows Store-alkalmazások][PlayerApplication]

További információ a Windows Store-alkalmazás fejlesztése: [fejleszthet nagyszerű alkalmazásokat a Windows 8](https://msdn.microsoft.com/windows/apps/br229512.aspx). Ebben a leckében az alábbi eljárásokat tartalmazza:

1. A Windows Store-projekt létrehozása
2. A felhasználói felület (XAML) tervezése
3. Módosítsa a fájl mögötti kódban
4. Fordítsa le és az alkalmazás tesztelése

**A Windows Store-projekt létrehozása**

1. Futtassa a Visual Studio; 2012 keresztül 2015-verziókat támogatja.
2. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre.
3. Új projekt párbeszédpanelen írja be vagy válassza ki a következő értékeket:

| Name (Név) | Érték |
| --- | --- |
| Sablon csoport |Telepített/sablonok/Visual C#Windows Store |
| Sablon |Blank App (XAML) |
| Name (Név) |SSPlayer |
| Hely |C:\SSTutorials |
| Megoldás neve |SSPlayer |
| Könyvtár létrehozása a megoldáshoz |(kiválasztva) |

1. Kattintson az **OK** gombra.

**A Smooth Streaming Client SDK hivatkozás hozzáadása**

1. A Megoldáskezelőben kattintson a jobb gombbal **SSPlayer**, és kattintson a **hivatkozás hozzáadása**.
2. Írja be vagy válassza ki az alábbi értékeket:

| Name (Név) | Érték |
| --- | --- |
| Referencia-csoport |Windows/Extensions |
| Leírások |Válassza ki a Microsoft Smooth Streaming Client SDK Windows 8 és a Microsoft Visual C++ futásidejű csomag |

1. Kattintson az **OK** gombra. 

Miután hozzáadta a hivatkozásokat, ki kell választania a megcélzott platform (x64 vagy x86), hivatkozások hozzáadása nem fog működni bármilyen Processzor platform konfiguráció.  A megoldáskezelőben látni fogja, sárga figyelmeztető meg ezek hozzá hivatkozásokat.

**A player felhasználói felület tervezéséhez**

1. A Megoldáskezelőben kattintson duplán **MainPage.xaml** a tervezési nézetében való megnyitásához.
2. Keresse meg a **&lt;rács&gt;** és **&lt;/Grid&gt;** címkék az XAML-fájlt, és illessze be a két címke között az alábbi kódot:
   ```xml
         <Grid.RowDefinitions>

            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
         </Grid.RowDefinitions>

         <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
         </StackPanel>

         <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
         </StackPanel>

         <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
         </StackPanel>
   ```
   Médiatartalom lejátszása a prvku MediaElement vezérlő szolgál. A csúszka vezérlőelemmel sliderProgress nevű szabályozhatja az adathordozó folyamatban van a következő lecke lesz használható.
3. Nyomja meg **CTRL + S** szeretné menteni a fájlt.

A prvku MediaElement vezérlő nem támogatja a Smooth Streaming tartalom out-of-box. A Smooth Streaming-támogatás engedélyezése, regisztrálnia kell a Smooth Streaming bájt-stream kezelő fájlnév-kiterjesztésű és MIME-típus.  Történő regisztrálásához használja a MediaExtensionManager.RegisterByteStreamHandler metódus a Windows.Media névtér.

A XAML fájlban néhány eseménykezelők kapcsolódnak a vezérlőkkel.  Meg kell adnia ezeket eseménykezelők.

**Módosíthatja a fájl mögötti kódban**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. A fájl elejéhez adja hozzá a következő using utasítást:
   
        using Windows.Media;
3. Elején a **MainPage** osztályban adja hozzá a következő adatelem:
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. Végén a **MainPage** konstruktorhoz adja hozzá a következő sort:
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
5. A végén a **MainPage** osztály, illessze be a következő kódot:
   ```csharp
         # region UI Button Click Events
         private void btnPlay_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Play();
         txtStatus.Text = "MediaElement is playing ...";
         }
         private void btnPause_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Pause();
         txtStatus.Text = "MediaElement is paused";
         }
         private void btnSetSource_Click(object sender, RoutedEventArgs e)
         {

         sliderProgress.Value = 0;
         mediaElement.Source = new Uri(txtMediaSource.Text);

         if (chkAutoPlay.IsChecked == true)
         {
             txtStatus.Text = "MediaElement is playing ...";
         }
         else
         {
             txtStatus.Text = "Click the Play button to play the media source.";
         }
         }
         private void btnStop_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Stop();
         txtStatus.Text = "MediaElement is stopped";
         }
         private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
         {

         txtStatus.Text = "Seek to position " + sliderProgress.Value;
         mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
         }
         # endregion
   ```
A sliderProgress_PointerPressed eseménykezelő itt van definiálva.  Nincsenek működéséhez, ehhez további működik, amely ebben az oktatóanyagban a következő leckében tárgyalja.
6. Nyomja meg **CTRL + S** szeretné menteni a fájlt.

A kész fájl mögötti kódban kell kinéznie:

![A Visual Studio, Smooth Streaming Windows Store alkalmazás Codeview][CodeViewPic]

**Fordítsa le és az alkalmazás tesztelése**

1. Az a **hozhat létre** menüben kattintson a **Configuration Manager**.
2. Változás **aktív megoldás platformját** megfelelően a fejlesztési platform.
3. Nyomja meg **F6** összeállítása a projekt. 
4. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
5. Az alkalmazás tetején használja az alapértelmezett Smooth Streaming URL-címet, vagy adjon meg egy másikat. 
6. Kattintson a **Nastavit Zdroje**. Mivel **automatikus lejátszás** engedélyezve van alapértelmezés szerint az adathordozót kell lejátszása automatikusan.  Az adathordozó segítségével szabályozhatja a **lejátszása**, **szüneteltetése** és **leállítása** gombok.  Szabályozhatja, hogy a média kötetet a függőleges csúszkát.  Azonban a vízszintes csúszka szabályozni a media folyamatban van a teljes körűen még nem használható. 

Lesson1 befejeződött.  Ebben a leckében használhatja egy prvku MediaElement vezérlőelem lejátszás Smooth Streaming-tartalmat.  A következő leckében hozzáadja egy csúszkát a Smooth Streaming-tartalmat állapotának szabályozásához.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>2. lecke: Adjon hozzá egy csúszka, szabályozhatja az adathordozó folyamatban

1. lecke létrehozott egy Windows Store-alkalmazás lejátszási Smooth Streaming-tartalmat prvku MediaElement XAML-vezérlővel.  Néhány alapvető adathordozó-funkciók, például indítása, leállítása és szüneteltetése származik.  Ebben a leckében egy csúszkavezérlő sáv fog hozzáadni az alkalmazáshoz.

Ebben az oktatóanyagban a csúszka pozíció alapján a jelenlegi pozíció ovládacího prvku MediaElement frissíteni egy időzítő használjuk.  A csúszka kezdési és befejezési időt is frissíteni kell az élő tartalmak esetén.  Ez jobban kezelhető az adaptív forrás update eseményt.

Továbbítási források olyan objektumok, amelyek hozhat létre media adatokat.  A forrás-feloldó egy URL-cím vagy bájt stream fogadja, és létrehozza a megfelelő adatforrást az adott tartalomhoz.  A forrás-feloldó szabványos módja a továbbítási források létrehozásához az alkalmazásokhoz. 

Ebben a leckében az alábbi eljárásokat tartalmazza:

1. A Smooth Streaming-kezelő regisztrálása 
2. Az adaptív forrás manager szintű eseménykezelők hozzáadása
3. Az adaptív forrás eseménykezelők hozzáadása
4. O MediaElement eseménykezelők hozzáadása
5. Csúszka kapcsolódó kód hozzáadása
6. Fordítsa le és az alkalmazás tesztelése

**A Smooth Streaming bájt-adatfolyam-kezelő regisztrálása, majd továbbítja a propertyset**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. A fájl elején adja hozzá a következő using utasítást:

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. A MainPage osztály elején adja hozzá a következő adatok tagok:

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. Belül a **MainPage** konstruktor, hozzáadása után a következő kódot a **ez. Components(); inicializálása**  vonal- és a regisztrációs kód az előző leckét írt sorok:

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. Belül a **MainPage** konstruktor, módosítsa a két RegisterByteStreamHandler vehetők fel a rögzített paraméterek:

   ```csharp
         // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
         // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
         // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
         propertySet);
   ```
6. Nyomja meg **CTRL + S** szeretné menteni a fájlt.

**Az adaptív forrás manager szintű eseménykezelő**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. Belül a **MainPage** osztályban adja hozzá a következő adatelem:

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. A végén a **MainPage** osztályban adja hozzá a következő eseménykezelő:

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. A végén a **MainPage** konstruktorhoz adja hozzá a következő sort az adaptív forrás nyílt esemény előfizetni:

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. Nyomja meg **CTRL + S** szeretné menteni a fájlt.

**Az adaptív forrás szintű eseménykezelők hozzáadása**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. Belül a **MainPage** osztályban adja hozzá a következő adatelem:

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. A végén a **MainPage** osztályban adja hozzá a következő esemény-kezelők:

   ```csharp
         # region Adaptive Source Level Events
         private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
         }

         private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
         {

            adaptiveSourceStatusUpdate = args;
         }

         private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
         {

            txtStatus.Text = "Error: " + args.HttpResponse;
         }

         # endregion Adaptive Source Level Events
   ```
4. Végén a **prvku mediaElement AdaptiveSourceOpened** metódus, a következő kódot a feliratkozás az esemény hozzáadása:

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. Nyomja meg **CTRL + S** szeretné menteni a fájlt.

Ugyanazokat az eseményeket adaptív forrás Manager szinten is, amely az alkalmazás összes médiaelemek közös funkciók kezeléséhez használható érhetők el. Minden egyes AdaptiveSource magában foglalja a saját eseményeket, és minden AdaptiveSource események átkerül AdaptiveSourceManager alatt.

**Médiaelem eseménykezelők hozzáadása**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. A végén a **MainPage** osztályban adja hozzá a következő esemény-kezelők:

   ```csharp
         # region Media Element Event Handlers
         private void MediaOpened(object sender, RoutedEventArgs e)
         {

            txtStatus.Text = "MediaElement opened";
         }

         private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
         {

            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
         }

         private void MediaEnded(object sender, RoutedEventArgs e)
         {

            txtStatus.Text ="MediaElement ended.";
         }

         # endregion Media Element Event Handlers
   ```
3. A végén a **MainPage** konstruktort, azokhoz az eseményekhez dolní index je ad hozzá a következő kódot:

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. Nyomja meg **CTRL + S** szeretné menteni a fájlt.

**Adja hozzá a csúszka sávjának kapcsolódó kód**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. A fájl elején adja hozzá a következő using utasítást:

   ```csharp
        using Windows.UI.Core;
   ```
3. Belül a **MainPage** osztályban adja hozzá a következő adatok tagok:

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. A végén a **MainPage** konstruktorhoz adja hozzá a következő kódot:

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. A végén a **MainPage** osztályhoz, adja hozzá a következő kódot:

   ```csharp
         # region sliderMediaPlayer
         private double SliderFrequency(TimeSpan timevalue)
         {

            long absvalue = 0;
            double stepfrequency = -1;

            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }

            TimeSpan totalDVRDuration = new TimeSpan(absvalue);

            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }

            return stepfrequency;
         }

         void updateSliderPositionoNTicks(object sender, object e)
         {

            sliderProgress.Value = mediaElement.Position.TotalSeconds;
         }

         public void setupTimer()
         {

            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
         }

         public void startTimer()
         {

            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderStartTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderEndTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
         }

         # endregion sliderMediaPlayer
   ```

   > [!NOTE]
   > Módosíthatja a felhasználói felület szála nem UI-szálból CoreDispatcher szolgál. Esetén szűk keresztmetszet dispatcher szálon fejlesztői is választható hallgatója kíván frissíteni a felhasználói felületi elem által biztosított dispatcher.  Példa:

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. A végén a **mediaElement_AdaptiveSourceStatusUpdated** metódust, adja hozzá a következő kódot:

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. A végén a **MediaOpened** metódust, adja hozzá a következő kódot:

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. Nyomja meg **CTRL + S** szeretné menteni a fájlt.

**Fordítsa le és az alkalmazás tesztelése**

1. Nyomja meg **F6** összeállítása a projekt. 
2. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. Az alkalmazás tetején használja az alapértelmezett Smooth Streaming URL-címet, vagy adjon meg egy másikat. 
4. Kattintson a **Nastavit Zdroje**. 
5. A csúszka sávjának teszteléséhez.

2. lecke befejeződött.  Ebben a leckében hozzáadott alkalmazás egy csúszkát. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>3. lecke: Jelölje be a Streamek Smooth Streaming
Smooth Streaming stream tartalom több nyelven hangsáv, amelyek a nézők által választható kezelésére képes.  Ebben a leckében lehetővé teszi a nézők jelölje be a Streamek. Ebben a leckében az alábbi eljárásokat tartalmazza:

1. Az XAML-fájl módosítása
2. Módosítsa a fájl mögötti kódban
3. Fordítsa le és az alkalmazás tesztelése

**Az XAML-fájl módosítása**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **adatforrásnézet-tervezőből**.
2. Keresse meg &lt;Grid.RowDefinitions&gt;, módosítsa a RowDefinitions, úgy hogy néz ki:

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. Belül a &lt;rács&gt;&lt;/Grid&gt; címkéket, adja hozzá a következő kódot egy lista vezérlőelem határozza meg, így a felhasználók elérhető Streamek listájának megtekintéséhez, vagy jelölje be a Streamek:

   ```xml
         <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
         </Grid>
   ```
4. Nyomja meg **CTRL + S** menti a módosításokat.

**Módosíthatja a fájl mögötti kódban**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. Az SSPlayer névtéren belül adjon hozzá egy új osztályt:

   ```csharp
        #region class Stream
   
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
   
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
   
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
   
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream
   ```
3. A MainPage osztály elején adja hozzá a következő változó definíciókat:

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. A MainPage osztály belül adja hozzá a következő régióban:
   ```csharp
        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
   
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
   
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
   
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
   
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
   
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
   
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
   
            // Select the first video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
   
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
   
            // Select the first audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
   
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
   
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection
   ```
5. Keresse meg a mediaElement_ManifestReady módszer, fűzze hozzá a következő kódot a függvény végén:
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    Így ha o MediaElement jegyzékfájl készen áll, a kód a rendelkezésre álló adatfolyamok listáját kéri le, és feltölti a felhasználói felület lista mezőt a lista.
6. Az MainPage osztály keresse meg a felhasználói felület gombok események régió kattintson, és adja a következő függvény definíciója:
   ```csharp
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }
   ```
**Fordítsa le és az alkalmazás tesztelése**

1. Nyomja meg **F6** összeállítása a projekt. 
2. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. Az alkalmazás tetején használja az alapértelmezett Smooth Streaming URL-címet, vagy adjon meg egy másikat. 
4. Kattintson a **Nastavit Zdroje**. 
5. Az alapértelmezett nyelv az audio_eng. Próbálja meg audio_eng és audio_es közötti váltáshoz. Minden alkalommal egy új adatfolyamot választja, kattintson a Küldés gombra.

3. lecke befejeződött.  Ebben a leckében a Streamek kiválasztása funkciókat adhat meg.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>4. lecke: Válassza ki a Smooth Streaming nyomon követi
Smooth Streaming bemutató különböző minőségi szintet (átviteli sebességek) és a megoldásuk kódolású, több videó fájlokat tartalmazza. Ebben a leckében, lehetővé teszi a felhasználóknak, hogy nyomon követi. Ebben a leckében az alábbi eljárásokat tartalmazza:

1. Az XAML-fájl módosítása
2. Módosítsa a fájl mögötti kódban
3. Fordítsa le és az alkalmazás tesztelése

**Az XAML-fájl módosítása**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **adatforrásnézet-tervezőből**.
2. Keresse meg a &lt;rács&gt; nevű címke **gridStreamAndBitrateSelection**, fűzze hozzá a következő kódot a címke végén:
   ```xml
         <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
         </StackPanel>
   ```
3. Nyomja meg **CTRL + S** he módosítások mentése

**Módosíthatja a fájl mögötti kódban**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. Az SSPlayer névtéren belül adjon hozzá egy új osztályt:
   ```csharp
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
   
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
   
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
   
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track
   ```
3. A MainPage osztály elején adja hozzá a következő változó definíciókat:
   ```csharp
        private List<Track> availableTracks;
   ```
4. A MainPage osztály belül adja hozzá a következő régióban:
   ```csharp
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>
   
        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();
   
            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;
   
            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;
   
                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }
   
        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }
   
        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }
   
        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }
   
        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection
   ```
5. Keresse meg a mediaElement_ManifestReady módszer, fűzze hozzá a következő kódot a függvény végén:
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. Az MainPage osztály keresse meg a felhasználói felület gombok események régió kattintson, és adja a következő függvény definíciója:
   ```csharp
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }
   ```
**Fordítsa le és az alkalmazás tesztelése**

1. Nyomja meg **F6** összeállítása a projekt. 
2. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. Az alkalmazás tetején használja az alapértelmezett Smooth Streaming URL-címet, vagy adjon meg egy másikat. 
4. Kattintson a **Nastavit Zdroje**. 
5. Alapértelmezés szerint az összes a video-adatfolyamot, a számok ki van jelölve. Kísérlet a átviteli sebesség módosításokat, válassza ki a legalacsonyabb átviteli sebességet, és válassza ki a legnagyobb átviteli sebességet. Minden módosítás után kattintson a küldés.  Láthatja, hogy a videó minősége módosításokat.

4. lecke befejeződött.  Ebben a leckében kiválasztása nyomon követi a funkciókat adhat meg.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Egyéb erőforrások:
* [Hogyan lehet speciális funkciókkal Smooth Streaming Windows 8 JavaScript-alkalmazás létrehozása](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Smooth Streaming technikai áttekintése](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

