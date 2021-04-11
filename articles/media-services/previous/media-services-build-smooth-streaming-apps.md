---
title: Smooth Streaming Windows áruházbeli alkalmazás oktatóanyaga | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Azure Media Services egy C# Windows áruházbeli alkalmazás létrehozásához egy XML-MediaElement vezérlőelem használatával a zökkenőmentes adatfolyam-tartalmak lejátszásához.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 20c7068fa0cb56699fdbc6d75b279abadd583832
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017120"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Smooth Streaming Windows áruházbeli alkalmazás létrehozása

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

A Windows 8 rendszerhez készült Smooth Streaming ügyfél-SDK lehetővé teszi a fejlesztők számára, hogy olyan Windows áruházbeli alkalmazásokat hozzanak létre, amelyek igény szerinti és élő Smooth Streaming tartalmakat tudnak lejátszani A Smooth Streaming tartalom alapszintű lejátszásán kívül az SDK olyan gazdag funkciókat is kínál, mint például a Microsoft PlayReady-védelem, a minőségi szint korlátozása, az élő DVR, a hangadatfolyam-váltás, az állapot frissítéseinek figyelése (például a minőségi szintű változások) és a hibák. A támogatott funkciókkal kapcsolatos további információkért tekintse meg a [kibocsátási megjegyzéseket](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). További információ: [Windows 8 Player Framework](https://playerframework.codeplex.com/). 

Ez az oktatóanyag négy leckét tartalmaz:

1. Alapszintű Smooth Streaming áruházbeli alkalmazás létrehozása
2. Csúszka hozzáadása a média előrehaladásának szabályozásához
3. Smooth Streaming streamek kiválasztása
4. Smooth Streaming zeneszámok kiválasztása

## <a name="prerequisites"></a>Előfeltételek
> [!NOTE]
> A Windows áruházbeli projektek 8,1-es és korábbi verziói nem támogatottak a Visual Studio 2017-ben.  További információ: [A Visual Studio 2017 platform célcsoportkezelése és kompatibilitása](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

* Windows 8 32 bites vagy 64 bites.
* A Visual Studio 2012-es verziójával 2015.
* [Microsoft Smooth streaming ÜGYFÉLOLDALI SDK Windows 8](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home)rendszerhez.

Az összes lecke befejezett megoldását letöltheti az MSDN Developer Code Samples (code Gallery) használatával: 

* [1. lecke](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) – egy egyszerű Windows 8 Smooth Streaming Media Player, 
* [2. lecke](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) – egy egyszerű Windows 8 Smooth Streaming Media Player egy csúszkás vezérlővel, 
* [3. lecke](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) – Windows 8 Smooth Streaming Media Player stream-kijelöléssel  
* [4. lecke](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907)  – Windows 8 Smooth Streaming Media Player a nyomon követési kijelöléssel.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>1. lecke: alapszintű Smooth Streaming áruházbeli alkalmazás létrehozása

Ebben a leckében egy MediaElement-vezérlővel rendelkező Windows áruházbeli alkalmazást hoz létre a Smooth stream-tartalom lejátszásához.  A futó alkalmazás a következőképpen néz ki:

![Példa Smooth Streaming Windows áruházbeli alkalmazásra][PlayerApplication]

A Windows áruházbeli alkalmazások fejlesztésével kapcsolatos további információkért tekintse meg a [Windows 8 rendszerhez készült nagyszerű alkalmazások fejlesztése](https://developer.microsoft.com/en-us/windows/)című témakört. Ez a lecke az alábbi eljárásokat tartalmazza:

1. Windows áruházbeli projekt létrehozása
2. A felhasználói felület megtervezése (XAML)
3. A fájl mögötti kód módosítása
4. Az alkalmazás fordítása és tesztelése

### <a name="to-create-a-windows-store-project"></a>Windows áruházbeli projekt létrehozása

1. A Visual Studio futtatása; a 2012 és 2015 közötti verziók támogatottak.
1. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre.
1. Az új projekt párbeszédpanelen írja be vagy válassza ki a következő értékeket:

    | Name | Érték |
    | --- | --- |
    | Sablon csoport |Telepített/sablonok/Visual C#/Windows Store |
    | Sablon |Üres alkalmazás (XAML) |
    | Name |SSPlayer |
    | Hely |C:\SSTutorials |
    | Megoldás neve |SSPlayer |
    | Könyvtár létrehozása a megoldáshoz |kiválasztott |

1. Kattintson az **OK** gombra.

### <a name="to-add-a-reference-to-the-smooth-streaming-client-sdk"></a>Hivatkozás hozzáadása az Smooth Streaming ügyféloldali SDK-hoz

1. Megoldáskezelő kattintson a jobb gombbal a **SSPlayer** elemre, majd kattintson a **hivatkozás hozzáadása** parancsra.
1. Írja be vagy válassza ki az alábbi értékeket:

    | Name | Érték |
    | --- | --- |
    | Hivatkozási csoport |Windows/bővítmények |
    | Referencia |Válassza a Microsoft Smooth Streaming ügyféloldali SDK Windows 8 és a Microsoft Visual C++ futtatókörnyezethez csomagot |

1. Kattintson az **OK** gombra. 

A hivatkozások hozzáadása után ki kell választania a célként megadott platformot (x64 vagy x86), a hivatkozások hozzáadásával nem fog működni a CPU-platform konfigurációjában.  A megoldás Explorerben sárga figyelmeztető jelzés jelenik meg ezekhez a hozzáadott hivatkozásokhoz.

### <a name="to-design-the-player-user-interface"></a>A lejátszó felhasználói felületének megtervezése

1. A Megoldáskezelő kattintson duplán a **Főoldal. XAML** elemre a tervezési nézetben való megnyitásához.
2. Keresse meg a **&lt; Grid &gt;** és a **&lt; /Grid &gt;** címkét a XAML-fájlban, és illessze be a következő kódot a két címke közé:

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
            <TextBox x:Name="txtMediaSource" Text="https://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
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
   A MediaElement vezérlő az adathordozó lejátszására szolgál. A sliderProgress nevű csúszkát a következő leckében fogjuk használni a média előrehaladásának szabályozásához.
3. Nyomja le a **CTRL + S** billentyűkombinációt a fájl mentéséhez.

A MediaElement vezérlő nem támogatja a Smooth Streaming a tartalmat. A Smooth Streaming támogatásának engedélyezéséhez a fájlnév-kiterjesztés és a MIME-típus szerint regisztrálnia kell a Smooth Streaming byte-stream kezelőt.  A regisztráláshoz használja a Windows. Media névtér MediaExtensionManager. RegisterByteStreamHandler metódusát.

Ebben a XAML-fájlban egyes eseménykezelők a vezérlőkhöz vannak társítva.  Ezeket az eseménykezelőket kell megadnia.

### <a name="to-modify-the-code-behind-file"></a>A fájl mögötti kód módosítása

1. Megoldáskezelő kattintson a jobb gombbal a **Főoldal. XAML** elemre, majd kattintson a **kód megtekintése** elemre.
2. A fájl tetején adja hozzá a következő using utasítást:

    ```csharp
        using Windows.Media;
    ```

3. A **Főoldal** osztály elején adja hozzá a következő adattagot:

    ```csharp
        private MediaExtensionManager extensions = new MediaExtensionManager();
    ```

4. A **Főoldal** konstruktorának végén adja hozzá a következő két sort:

    ```csharp
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
    ```

5. A **Főoldal** osztály végén illessze be a következő kódot:
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
   Az sliderProgress_PointerPressed eseménykezelő itt van definiálva.  A működésének megkezdéséhez több tennivaló is van, amelyeket az oktatóanyag következő leckéje tárgyal.
6. Nyomja le a **CTRL + S** billentyűkombinációt a fájl mentéséhez.

A fájl mögötti kód befejezése a következőképpen fog kinézni:

![Codeview a Visual Studióban Smooth Streaming Windows áruházbeli alkalmazáshoz][CodeViewPic]

### <a name="to-compile-and-test-the-application"></a>Az alkalmazás fordítása és tesztelése

1. A **Build** menüben kattintson a **Configuration Manager** elemre.
2. Módosítsa az **aktív megoldási platformot** úgy, hogy az megfeleljen a fejlesztői platformnak.
3. A projekt fordításához nyomja le az **F6** billentyűt. 
4. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
5. Az alkalmazás tetején használhatja az alapértelmezett Smooth Streaming URL-címet, vagy megadhat egy másikat. 
6. Kattintson a **forrás beállítása** elemre. Mivel az **automatikus lejátszás** alapértelmezés szerint engedélyezve van, az adathordozó automatikusan lejátszásra kerül.  Az adathordozót a **Play**, a **pause** és a **stop** gomb használatával szabályozhatja.  Az adathordozó kötetét a függőleges csúszka használatával szabályozhatja.  A média előrehaladásának szabályozására szolgáló vízszintes csúszka azonban még nincs teljesen implementálva. 

Befejezte a lesson1.  Ebben a leckében egy MediaElement vezérlőelemet használ a Smooth Streaming tartalmának lejátszásához.  A következő leckében hozzáad egy csúszkát a Smooth Streaming tartalom állapotának szabályozásához.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>2. lecke: görgetősáv hozzáadása a média előrehaladásának szabályozásához

Az 1. leckében létrehozott egy Windows áruházbeli alkalmazást, amely egy MediaElement-XAML vezérlővel rendelkezik, amely a médiafájlok lejátszását Smooth Streaming.  Olyan alapszintű adathordozó-funkciókat tartalmaz, mint az indítás, a leállítás és a Szüneteltetés.  Ebben a leckében egy csúszka vezérlőelemet fog hozzáadni az alkalmazáshoz.

Ebben az oktatóanyagban egy időzítővel frissítjük a csúszka pozícióját a MediaElement vezérlő aktuális pozíciója alapján.  Élő tartalom esetén a csúszka indítási és befejezési időpontját is frissíteni kell.  Ez jobban kezelhető az adaptív forrás frissítési eseményében.

A Media sources olyan objektumok, amelyek adathordozó-adatforrást hoznak.  A forrás-feloldó egy URL-címet vagy egy bájtos adatfolyamot hoz létre, és létrehozza a tartalomhoz szükséges adathordozó-forrást.  A forrás-feloldó a szabványos módszer, amellyel az alkalmazások adathordozó-forrásokat hozhatnak létre. 

Ez a lecke az alábbi eljárásokat tartalmazza:

1. A Smooth Streaming kezelő regisztrálása 
2. Az adaptív forrás-kezelő szintű eseménykezelők hozzáadása
3. Az adaptív forrás szintű eseménykezelők hozzáadása
4. MediaElement-eseménykezelők hozzáadása
5. Csúszka-sáv kapcsolódó kódjának hozzáadása
6. Az alkalmazás fordítása és tesztelése

### <a name="to-register-the-smooth-streaming-byte-stream-handler-and-pass-the-propertyset"></a>A Smooth Streaming byte-stream kezelő regisztrálása és a propertyset továbbítása

1. Megoldáskezelő kattintson a jobb gombbal a **Főoldal. XAML** elemre, majd kattintson a **kód megtekintése** elemre.
2. A fájl elején adja hozzá a következő using utasítást:

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. A Főoldal osztály elején adja hozzá a következő adattagokat:

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. A **Főoldal** konstruktorában adja hozzá a következő kódot a **this.Initialize Components ();** sor és az előző leckében írt regisztrációs kód sorai után:

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. A **Főoldal** -konstruktoron belül módosítsa a két RegisterByteStreamHandler metódust a Forth paraméterek hozzáadásához:

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
6. Nyomja le a **CTRL + S** billentyűkombinációt a fájl mentéséhez.

### <a name="to-add-the-adaptive-source-manager-level-event-handler"></a>Az adaptív forrás-kezelő szintű eseménykezelő hozzáadása

1. Megoldáskezelő kattintson a jobb gombbal a **Főoldal. XAML** elemre, majd kattintson a **kód megtekintése** elemre.
2. A **Főoldal** osztályon belül adja hozzá a következő adattagot:

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. A **Főoldal** osztály végén adja hozzá a következő eseménykezelőt:

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. A **Főoldal** konstruktorának végén adja hozzá a következő sort az adaptív forrás nyitott eseményre való előfizetéshez:

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. Nyomja le a **CTRL + S** billentyűkombinációt a fájl mentéséhez.

### <a name="to-add-adaptive-source-level-event-handlers"></a>Adaptív forrás szintű eseménykezelők hozzáadása

1. Megoldáskezelő kattintson a jobb gombbal a **Főoldal. XAML** elemre, majd kattintson a **kód megtekintése** elemre.
2. A **Főoldal** osztályon belül adja hozzá a következő adattagot:

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. A **Főoldal** osztály végén adja hozzá a következő eseménykezelőket:

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
4. A **MediaElement AdaptiveSourceOpened** metódus végén adja hozzá a következő kódot az eseményekre való előfizetéshez:

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. Nyomja le a **CTRL + S** billentyűkombinációt a fájl mentéséhez.

Ugyanezek az események az adaptív forrású jászol szintjén is elérhetők, amely az alkalmazás összes multimédiás eleméhez közös funkciók kezelésére használható. Minden AdaptiveSource magában foglalja a saját eseményeit, és az összes AdaptiveSource-eseményt a AdaptiveSourceManager alatt lépcsőzetesen kell megadnia.

### <a name="to-add-media-element-event-handlers"></a>Media Element eseménykezelők hozzáadása

1. Megoldáskezelő kattintson a jobb gombbal a **Főoldal. XAML** elemre, majd kattintson a **kód megtekintése** elemre.
2. A **Főoldal** osztály végén adja hozzá a következő eseménykezelőket:

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
3. A **Főoldal** konstruktorának végén adja hozzá a következő kódot az alszkripthez az eseményekhez:

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. Nyomja le a **CTRL + S** billentyűkombinációt a fájl mentéséhez.

### <a name="to-add-slider-bar-related-code"></a>A csúszka kapcsolódó kódjának hozzáadása

1. Megoldáskezelő kattintson a jobb gombbal a **Főoldal. XAML** elemre, majd kattintson a **kód megtekintése** elemre.
2. A fájl elején adja hozzá a következő using utasítást:

   ```csharp
        using Windows.UI.Core;
   ```
3. A **Főoldal** osztályon belül adja hozzá a következő adattagokat:

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. A **Főoldal** konstruktorának végén adja hozzá a következő kódot:

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. A **Főoldal** osztály végén adja hozzá a következő kódot:

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
   > A CoreDispatcher a felhasználói felületi szál nem a felhasználói felületi szálból történő módosítására szolgál. Ha szűk keresztmetszetre van szükség a diszpécser szálon, a fejlesztő dönthet úgy, hogy a felhasználói felületi elem által biztosított diszpécsert használja.  Például:

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. A **mediaElement_AdaptiveSourceStatusUpdated** metódus végén adja hozzá a következő kódot:

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. A **MediaOpened** metódus végén adja hozzá a következő kódot:

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. Nyomja le a **CTRL + S** billentyűkombinációt a fájl mentéséhez.

### <a name="to-compile-and-test-the-application"></a>Az alkalmazás fordítása és tesztelése

1. A projekt fordításához nyomja le az **F6** billentyűt. 
2. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. Az alkalmazás tetején használhatja az alapértelmezett Smooth Streaming URL-címet, vagy megadhat egy másikat. 
4. Kattintson a **forrás beállítása** elemre. 
5. Tesztelje a csúszkát.

Elvégezte a 2. lecke elvégzését.  Ebben a leckében egy csúszkát adott hozzá az alkalmazáshoz. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>3. lecke: Smooth Streaming streamek kiválasztása
A Smooth Streaming képes a tartalmak több, a nézők által kiválasztható hangsávokkal történő továbbítására.  Ebben a leckében engedélyezi a nézők számára a streamek kiválasztását. Ez a lecke az alábbi eljárásokat tartalmazza:

1. A XAML fájl módosítása
2. A fájl mögötti kód módosítása
3. Az alkalmazás fordítása és tesztelése

### <a name="to-modify-the-xaml-file"></a>A XAML fájl módosítása

1. Megoldáskezelő kattintson a jobb gombbal a **Főoldal. XAML** elemre, majd kattintson a **tervező megtekintése** elemre.
2. Keresse meg &lt; a Grid. RowDefinitions &gt; , és módosítsa a RowDefinitions úgy, hogy a következőképpen néznek ki:

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. A &lt; Grid &gt; &lt; /Grid &gt; címkén belül adja hozzá a következő kódot a lista vezérlőelem definiálásához, így a felhasználók láthatják az elérhető streamek listáját, és kiválaszthatják a streameket:

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
4. Nyomja le a **CTRL + S** billentyűkombinációt a módosítások mentéséhez.

### <a name="to-modify-the-code-behind-file"></a>A fájl mögötti kód módosítása

1. Megoldáskezelő kattintson a jobb gombbal a **Főoldal. XAML** elemre, majd kattintson a **kód megtekintése** elemre.
2. A SSPlayer névtérben adjon hozzá egy új osztályt:

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
3. A Főoldal osztály elején adja hozzá a következő változó definíciókat:

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. A Főoldal osztályban adja hozzá a következő régiót:
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
5. Keresse meg a mediaElement_ManifestReady metódust, fűzze hozzá a következő kódot a függvény végén:
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    Így ha a MediaElement-jegyzékfájl elkészült, a kód beolvassa az elérhető streamek listáját, és feltölti a felhasználói felületi listát a listához.
6. A Főoldal osztályban keresse meg a felhasználói felület gombjait, kattintson az események terület elemre, majd adja hozzá a következő függvény definícióját:
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

### <a name="to-compile-and-test-the-application"></a>Az alkalmazás fordítása és tesztelése

1. A projekt fordításához nyomja le az **F6** billentyűt. 
2. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. Az alkalmazás tetején használhatja az alapértelmezett Smooth Streaming URL-címet, vagy megadhat egy másikat. 
4. Kattintson a **forrás beállítása** elemre. 
5. Az alapértelmezett nyelv a audio_eng. Próbáljon váltani audio_eng és audio_es között. Minden alkalommal, amikor új streamet választ, a Küldés gombra kell kattintania.

Elvégezte a 3. leckét.  Ebben a leckében hozzáadja a funkciót a streamek kiválasztásához.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>4. lecke: Smooth Streaming zeneszámok kiválasztása

Egy Smooth Streaming bemutató több, különböző minőségi szinttel (átviteli sebességgel) kódolt videofájlokat és felbontást is tartalmazhat. Ebben a leckében lehetővé teszi a felhasználók számára a sávok kiválasztását. Ez a lecke az alábbi eljárásokat tartalmazza:

1. A XAML fájl módosítása
2. A fájl mögötti kód módosítása
3. Az alkalmazás fordítása és tesztelése

### <a name="to-modify-the-xaml-file"></a>A XAML fájl módosítása

1. Megoldáskezelő kattintson a jobb gombbal a **Főoldal. XAML** elemre, majd kattintson a **tervező megtekintése** elemre.
2. Keresse meg &lt; a &gt; **gridStreamAndBitrateSelection** nevű rács címkét, fűzze hozzá a következő kódot a címke végéhez:
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
3. Nyomja le a **CTRL + S** billentyűkombinációt a módosítások mentéséhez

### <a name="to-modify-the-code-behind-file"></a>A fájl mögötti kód módosítása

1. Megoldáskezelő kattintson a jobb gombbal a **Főoldal. XAML** elemre, majd kattintson a **kód megtekintése** elemre.
2. A SSPlayer névtérben adjon hozzá egy új osztályt:
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
3. A Főoldal osztály elején adja hozzá a következő változó definíciókat:
   ```csharp
        private List<Track> availableTracks;
   ```
4. A Főoldal osztályban adja hozzá a következő régiót:
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
5. Keresse meg a mediaElement_ManifestReady metódust, fűzze hozzá a következő kódot a függvény végén:
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. A Főoldal osztályban keresse meg a felhasználói felület gombjait, kattintson az események terület elemre, majd adja hozzá a következő függvény definícióját:
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
   
### <a name="to-compile-and-test-the-application"></a>Az alkalmazás fordítása és tesztelése

1. A projekt fordításához nyomja le az **F6** billentyűt. 
2. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. Az alkalmazás tetején használhatja az alapértelmezett Smooth Streaming URL-címet, vagy megadhat egy másikat. 
4. Kattintson a **forrás beállítása** elemre. 
5. Alapértelmezés szerint a videó stream összes nyomon követése ki van választva. Az átviteli sebesség változásának kipróbálásához kiválaszthatja az elérhető legalacsonyabb átviteli sebességet, majd kiválaszthatja az elérhető legmagasabb átviteli sebességet. Minden módosítás után kattintson a Submit (elküldés) gombra.  Láthatja a videó minőségének változásait.

Elvégezte a 4. leckét.  Ebben a leckében hozzáadja a funkciót a zeneszámok kiválasztásához.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Egyéb erőforrások:
* [Smooth Streaming Windows 8 JavaScript-alkalmazás létrehozása speciális funkciókkal](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Smooth Streaming technikai áttekintés](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
