---
title: Integrálás ügyfélalkalmazással a Speech SDK használatával
titleSuffix: Azure Cognitive Services
description: a UWP alkalmazásban futó Speech SDK által közzétett egyéni parancsok alkalmazásra vonatkozó kérések elvégzése.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: fa3a6d16b79800043bdcd3f183dd86fa278dd1a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026027"
---
# <a name="integrate-with-a-client-application-using-speech-sdk"></a>Integráció egy ügyfélalkalmazás használatával a Speech SDK-val

Ebből a cikkből megtudhatja, hogyan teheti közzé a közzétett egyéni parancsok alkalmazásait egy UWP-alkalmazásban futó Speech SDK-ból. Ahhoz, hogy kapcsolatot hozzon létre az egyéni parancsok alkalmazással, a következőkre lesz szüksége:

- Egyéni parancsok alkalmazás közzététele és alkalmazás-azonosító beszerzése (alkalmazás-azonosító)
- Hozzon létre egy Univerzális Windows-platform (UWP) ügyfélalkalmazás a Speech SDK használatával, hogy lehetővé tegye az egyéni parancsok alkalmazással való kommunikációt

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtásához egyéni parancsokat tartalmazó alkalmazás szükséges. Ha még nem hozott létre egyéni parancsokat, a gyors útmutatókat követve a következőket teheti:
> [!div class = "checklist"]
> * [Egyéni parancsokat tartalmazó alkalmazás létrehozása](quickstart-custom-commands-application.md)

A következőkre is szüksége lesz:
> [!div class = "checklist"]
> * A [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) -es vagy újabb verziója. Ez az útmutató a Visual Studio 2019-alapú.
> * Egy Azure-előfizetési kulcs a Speech Serviceshez. [Szerezze be ingyen](overview.md#try-the-speech-service-for-free) , vagy hozza létre a [Azure Portal](https://portal.azure.com)
> * [Az eszköz fejlesztésének engedélyezése](/windows/uwp/get-started/enable-your-device-for-development)

## <a name="step-1-publish-custom-commands-application"></a>1. lépés: egyéni parancsok alkalmazásának közzététele

1. A korábban létrehozott egyéni parancsok alkalmazás megnyitása
1. Lépjen a **Beállítások** elemre, és válassza a **Luis erőforrás** elemet.
1. Ha az **előrejelzési erőforrás** nincs hozzárendelve, válasszon egy lekérdezés-előrejelző kulcsot, vagy hozzon létre újat

    Az alkalmazás közzététele előtt mindig szükség van a lekérdezés-előrejelzési kulcsra. További információ a LUIS-erőforrásokról: a [Luis-erőforrás](../luis/luis-how-to-azure-subscription.md) referenciája

1. Lépjen vissza a parancsok szerkesztéséhez, és válassza a **Közzététel** lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Az alkalmazás közzététele](media/custom-commands/setup-speech-sdk-publish-application.png)

1. Az alkalmazás AZONOSÍTÓjának másolása a közzétételi értesítésből későbbi használatra
1. A beszédfelismerési erőforrás kulcsának másolása későbbi használatra

## <a name="step-2-create-a-visual-studio-project"></a>2. lépés: Visual Studio-projekt létrehozása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>3. lépés: mintakód hozzáadása

Ebben a lépésben hozzáadjuk az alkalmazás felhasználói felületét meghatározó XAML-kódot, és hozzáadjuk a C#-kódot a megvalósítás mögött.

### <a name="xaml-code"></a>XAML-kód

Hozza létre az alkalmazás felhasználói felületét a XAML kód hozzáadásával.

1. A **megoldáskezelő** megnyitásához nyissa meg a `MainPage.xaml`

1. A tervező XAML-nézetében cserélje le a teljes tartalmat a következő kódrészletre:

   ```xml
   <Page
       x:Class="helloworld.MainPage"
       xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       xmlns:local="using:helloworld"
       xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
       xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
       mc:Ignorable="d"
       Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

       <Grid>
           <StackPanel Orientation="Vertical" HorizontalAlignment="Center"
                       Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
               <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"
                       Margin="0,10,10,0" Click="EnableMicrophone_ButtonClicked"
                       Height="35"/>
               <Button x:Name="ListenButton" Content="Talk"
                       Margin="0,10,10,0" Click="ListenButton_ButtonClicked"
                       Height="35"/>
               <StackPanel x:Name="StatusPanel" Orientation="Vertical"
                           RelativePanel.AlignBottomWithPanel="True"
                           RelativePanel.AlignRightWithPanel="True"
                           RelativePanel.AlignLeftWithPanel="True">
                   <TextBlock x:Name="StatusLabel" Margin="0,10,10,0"
                              TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                   <Border x:Name="StatusBorder" Margin="0,0,0,0">
                       <ScrollViewer VerticalScrollMode="Auto"
                                     VerticalScrollBarVisibility="Auto" MaxHeight="200">
                           <!-- Use LiveSetting to enable screen readers to announce
                                the status update. -->
                           <TextBlock
                               x:Name="StatusBlock" FontWeight="Bold"
                               AutomationProperties.LiveSetting="Assertive"
                               MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}"
                               Margin="10,10,10,20" TextWrapping="Wrap"  />
                       </ScrollViewer>
                   </Border>
               </StackPanel>
           </StackPanel>
           <MediaElement x:Name="mediaElement"/>
       </Grid>
   </Page>
   ```

A Tervező nézet frissül az alkalmazás felhasználói felületének megjelenítéséhez.

### <a name="c-code-behind-source"></a>C# kód a forrás mögött

Adja hozzá a forráskód mögötti forrást, hogy az alkalmazás a várt módon működjön. A kód mögötti forrás a következőket tartalmazza:

- `using`A `Speech` és a `Speech.Dialog` névterek kötelező utasításai
- Egyszerű implementáció a mikrofon elérésének biztosításához, amely a gomb kezelőjéhez van kötve
- Alapvető felhasználói felületi segítők az alkalmazásban lévő üzenetek és hibák megjelenítéséhez
- Az inicializálási kód elérési útjának kiinduló pontja, amelyet később fel kell tölteni
- A szöveg és a beszéd visszajátszására szolgáló segítő (folyamatos átvitel támogatása nélkül)
- Egy üres gomb kezelője a figyelés megkezdéséhez, amely később fel lesz töltve

Adja hozzá a forráskód mögötti forrást az alábbiak szerint:

1. A **megoldáskezelő** nyissa meg a kód mögötti forrásfájlt `MainPage.xaml.cs` (a alatt csoportosítva `MainPage.xaml` )

1. Cserélje le a fájl tartalmát a következő kódra: 

   ```csharp
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Audio;
   using Microsoft.CognitiveServices.Speech.Dialog;
   using System;
   using System.IO;
   using System.Text;
   using Windows.UI.Xaml;
   using Windows.UI.Xaml.Controls;
   using Windows.UI.Xaml.Media;

   namespace helloworld
   {
       public sealed partial class MainPage : Page
       {
           private DialogServiceConnector connector;

           private enum NotifyType
           {
               StatusMessage,
               ErrorMessage
           };

           public MainPage()
           {
               this.InitializeComponent();
           }

           private async void EnableMicrophone_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               bool isMicAvailable = true;
               try
               {
                   var mediaCapture = new Windows.Media.Capture.MediaCapture();
                   var settings =
                       new Windows.Media.Capture.MediaCaptureInitializationSettings();
                   settings.StreamingCaptureMode =
                       Windows.Media.Capture.StreamingCaptureMode.Audio;
                   await mediaCapture.InitializeAsync(settings);
               }
               catch (Exception)
               {
                   isMicAvailable = false;
               }
               if (!isMicAvailable)
               {
                   await Windows.System.Launcher.LaunchUriAsync(
                       new Uri("ms-settings:privacy-microphone"));
               }
               else
               {
                   NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
               }
           }

           private void NotifyUser(
               string strMessage, NotifyType type = NotifyType.StatusMessage)
           {
               // If called from the UI thread, then update immediately.
               // Otherwise, schedule a task on the UI thread to perform the update.
               if (Dispatcher.HasThreadAccess)
               {
                   UpdateStatus(strMessage, type);
               }
               else
               {
                   var task = Dispatcher.RunAsync(
                       Windows.UI.Core.CoreDispatcherPriority.Normal,
                       () => UpdateStatus(strMessage, type));
               }
           }

           private void UpdateStatus(string strMessage, NotifyType type)
           {
               switch (type)
               {
                   case NotifyType.StatusMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Green);
                       break;
                   case NotifyType.ErrorMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Red);
                       break;
               }
               StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text)
                   ? strMessage : "\n" + strMessage;

               if (!string.IsNullOrEmpty(StatusBlock.Text))
               {
                   StatusBorder.Visibility = Visibility.Visible;
                   StatusPanel.Visibility = Visibility.Visible;
               }
               else
               {
                   StatusBorder.Visibility = Visibility.Collapsed;
                   StatusPanel.Visibility = Visibility.Collapsed;
               }
               // Raise an event if necessary to enable a screen reader
               // to announce the status update.
               var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
               if (peer != null)
               {
                   peer.RaiseAutomationEvent(
                       Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
               }
           }

           // Waits for and accumulates all audio associated with a given
           // PullAudioOutputStream and then plays it to the MediaElement. Long spoken
           // audio will create extra latency and a streaming playback solution
           // (that plays audio while it continues to be received) should be used --
           // see the samples for examples of this.
           private void SynchronouslyPlayActivityAudio(
               PullAudioOutputStream activityAudio)
           {
               var playbackStreamWithHeader = new MemoryStream();
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
               playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
               playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

               byte[] pullBuffer = new byte[2056];

               uint lastRead = 0;
               do
               {
                   lastRead = activityAudio.Read(pullBuffer);
                   playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
               }
               while (lastRead == pullBuffer.Length);

               var task = Dispatcher.RunAsync(
                   Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                   mediaElement.SetSource(
                       playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                   mediaElement.Play();
               });
           }

           private void InitializeDialogServiceConnector()
           {
               // New code will go here
           }

           private async void ListenButton_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               // New code will go here
           }
       }
   }
   ```
    > [!NOTE]
    > Ha a következő hibaüzenet jelenik meg: "az objektum típusa nem hivatkozott szerelvényben van definiálva"
    > 1. A jobb ügyfél a megoldás.
    > 1. Válassza **a megoldás NuGet-csomagok kezelése** lehetőséget, majd válassza a **frissítések** lehetőséget. 
    > 1. Ha a frissítés listán a **Microsoft. NETCore. UniversalWindowsPlatform** található, frissítse a **Microsoft. NETCore. UniversalWindowsPlatform** verziót a legújabb verzióra.

1. Adja hozzá a következő kódot a metódus törzséhez `InitializeDialogServiceConnector`

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. Cserélje le a karakterláncokat, `YourApplicationId` `YourSpeechSubscriptionKey` és az `YourServiceRegion` alkalmazás, a beszédfelismerés és a [régió](regions.md) saját értékeivel

1. Fűzze hozzá a következő kódrészletet a metódus törzsének végéhez `InitializeDialogServiceConnector`

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
   connector.ActivityReceived += (sender, activityReceivedEventArgs) =>
   {
       NotifyUser(
           $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

       if (activityReceivedEventArgs.HasAudio)
       {
           SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
       }
   };

   // Canceled will be signaled when a turn is aborted or experiences an error condition
   connector.Canceled += (sender, canceledEventArgs) =>
   {
       NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
       if (canceledEventArgs.Reason == CancellationReason.Error)
       {
           NotifyUser(
               $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
       }
   };

   // Recognizing (not 'Recognized') will provide the intermediate recognized text
   // while an audio stream is being processed
   connector.Recognizing += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
   };

   // Recognized (not 'Recognizing') will provide the final recognized text
   // once audio capture is completed
   connector.Recognized += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
   };

   // SessionStarted will notify when audio begins flowing to the service for a turn
   connector.SessionStarted += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
   };

   // SessionStopped will notify when a turn is complete and
   // it's safe to begin listening again
   connector.SessionStopped += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
   };
   ```

1. Adja hozzá a következő kódrészletet a metódus törzséhez a `ListenButton_ButtonClicked` osztályban. `MainPage`

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. A menüsávban válassza a **fájl**  >  **Mentés az összes mentése** lehetőséget a módosítások mentéséhez

## <a name="try-it-out"></a>Próbálja ki

1. Az alkalmazás létrehozásához a menüsávon válassza a **Build**  >  **Build megoldás** elemet. A kód fordításának hiba nélkül kell végbe mennie.

1.   >  Az alkalmazás indításához válassza a hibakeresés **indítása hibakeresést** (vagy nyomja le az **F5** billentyűt). Megjelenik a **HelloWorld** ablak.

   ![Minta UWP virtuális asszisztensi alkalmazás C#-ban – gyors útmutató](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Válassza a **mikrofon engedélyezése** lehetőséget. Ha a hozzáférési engedély kérése megjelenik, válassza az **Igen** lehetőséget.

   ![Mikrofon-hozzáférési engedély kérése](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Válassza a **Talk** lehetőséget, és beszéljen egy angol kifejezéssel vagy mondattal az eszköz mikrofonjában. A beszéd a közvetlen vonalas beszéd csatornába kerül, és szövegbe kerül, amely az ablakban jelenik meg.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Útmutató: tevékenység küldése ügyfélalkalmazás számára (előzetes verzió)](./how-to-custom-commands-send-activity-to-client.md)