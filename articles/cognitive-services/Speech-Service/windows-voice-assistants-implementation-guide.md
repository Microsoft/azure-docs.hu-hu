---
title: Hangsegédek a Windowsban – a zárolás megvalósítására vonatkozó irányelvek
titleSuffix: Azure Cognitive Services
description: A hangfelismerő alkalmazások hangalapú aktiválásának és zárolási képességeinek megvalósításához szükséges útmutatás.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.custom: devx-track-csharp
ms.openlocfilehash: 92ab043d4fccbe0764e361eac6f71ef69a5963cb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939862"
---
# <a name="implementing-voice-assistants-on-windows"></a>Hangsegédek implementálása Windows rendszeren

Ez az útmutató végigvezeti a Windowson futó hangsegédek létrehozásának fontos megvalósítási részletein.

## <a name="implementing-voice-activation"></a>Hangaktiválás megvalósítása

[A környezet beállítása](how-to-windows-voice-assistants-get-started.md) és a [Hangaktiválás működésének](windows-voice-assistants-overview.md#how-does-voice-activation-work)megismerése után megkezdheti a hangalapú aktiválás megvalósítását a saját hangsegéd alkalmazásban.

### <a name="registration"></a>Regisztráció

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>Győződjön meg arról, hogy a mikrofon elérhető és elérhető, majd figyelje az állapotát

A MVA szüksége van egy mikrofonra, és elérhetővé válik, hogy képes legyen a hangalapú aktiválás észlelésére. A [AppCapability](/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability), a [DeviceWatcher](/uwp/api/windows.devices.enumeration.devicewatcher)és a [MediaCapture](/uwp/api/windows.media.capture.mediacapture) osztály használatával megkeresheti a mikrofon adatvédelmi hozzáférését, az eszköz jelenlétét és az eszköz állapotát (például Volume és Mute).

### <a name="register-the-application-with-the-background-service"></a>Az alkalmazás regisztrálása a háttérben futó szolgáltatásban

Ahhoz, hogy a MVA az alkalmazást a háttérben indítsa el, az alkalmazást regisztrálni kell a háttérben futó szolgáltatásban. A háttérben futó szolgáltatás regisztrálásával kapcsolatban [itt](/windows/uwp/launch-resume/register-a-background-task)talál teljes körű útmutatót.

### <a name="unlock-the-limited-access-feature"></a>A korlátozott hozzáférés funkció zárolásának feloldása

A hangsegéd funkció zárolásának feloldásához használja a Microsoft által biztosított korlátozott hozzáférésű szolgáltatás kulcsát. Ehhez használja a Windows SDK [LimitedAccessFeature](/uwp/api/windows.applicationmodel.limitedaccessfeatures) osztályát.

### <a name="register-the-keyword-for-the-application"></a>Az alkalmazáshoz tartozó kulcsszó regisztrálása

Az alkalmazásnak regisztrálnia kell magát, a kulcsszavas modelljét és nyelvét a Windowsban.

Első lépésként beolvassa a kulcsszó-érzékelőt. Ebben a mintakódban beolvasjuk az első detektort, de kiválaszthatja azt a kiválasztásával `configurableDetectors` .

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

A ActivationSignalDetector objektum lekérése után hívja `ActivationSignalDetector.CreateConfigurationAsync` meg a metódust a jel azonosítójával, a modell azonosítójával és a megjelenítendő névvel a kulcsszó regisztrálásához és az alkalmazás beolvasásához `ActivationSignalDetectionConfiguration` . A jelet és a modell azonosítóit a fejlesztőnek kell megadnia, és konzisztensnek kell lennie ugyanahhoz a kulcsszóhoz.

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>Annak ellenőrzése, hogy engedélyezve van-e a hangaktiválási beállítás

A Hangaktiválás használatához a felhasználónak engedélyeznie kell a hangaktiválást a rendszer számára, és engedélyeznie kell a hangaktiválást az alkalmazáshoz. A beállítás a Windows-beállítások hang aktiválása adatvédelmi beállítások részében található. Az alkalmazásban a hangaktiválási beállítás állapotának megtekintéséhez használja a `ActivationSignalDetectionConfiguration` kulcsszó regisztrációját. A [AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) mezőjében `ActivationSignalDetectionConfiguration` szerepel egy Enum érték, amely leírja a hangaktiválási beállítás állapotát.

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>ConversationalAgentSession beolvasása az alkalmazás MVA rendszerbe való regisztrálásához

A `ConversationalAgentSession` Windows SDK osztálya, amely lehetővé teszi, hogy az alkalmazás frissítse a Windowst az alkalmazás állapotával (tétlen, észlelés, figyelés, működés, beszéd), és fogadja az eseményeket, például az aktiválás észlelését és a rendszerállapot-módosításokat, például a képernyő zárolását. A AgentSession egy példányának beolvasása azt is szolgálja, hogy a hang alapján aktiválja az alkalmazást a Windowsban. Az ajánlott eljárás az, hogy fenntartsa az egyik referenciáját `ConversationalAgentSession` . A munkamenet lekéréséhez használja az `ConversationalAgentSession.GetCurrentSessionAsync` API-t.

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>Hallgassa meg a két aktiváló jelet: a OnBackgroundActivated és a OnSignalDetected

A Windows akkor fogja jelezni az alkalmazást, ha a kulcsszót kétféleképpen észleli. Ha az alkalmazás nem aktív (azaz nincs hivatkozása egy nem eldobott példányra `ConversationalAgentSession` ), akkor elindítja az alkalmazást, és meghívja az OnBackgroundActivated metódust az alkalmazás app.XAML.cs-fájljában. Ha az Event argumentumok `BackgroundActivatedEventArgs.TaskInstance.Task.Name` mező megfelel a "AgentBackgroundTrigger" karakterláncnak, az alkalmazás indítását a Hangaktiválás aktiválja. Az alkalmazásnak felül kell bírálnia ezt a metódust, és le kell kérnie a ConversationalAgentSession egy példányát, hogy jelezze a most aktív Windows rendszernek. Ha az alkalmazás aktív, a Windows az eseményt használva jelzi a Hangaktiválás előfordulását `ConversationalAgentSession.OnSignalDetected` . A lekérése után vegyen fel egy eseménykezelőt erre az eseményre `ConversationalAgentSession` .

## <a name="keyword-verification"></a>Kulcsszó ellenőrzése

Ha a hangügynök alkalmazást a hang aktiválja, a következő lépés annak ellenőrzése, hogy a kulcsszó észlelése érvényes volt-e. A Windows nem biztosít megoldást a kulcsszó-ellenőrzésre, de lehetővé teszi a hangsegédek számára, hogy a hipotézises aktiválásból és a teljes ellenőrzésen keresztül hozzáférjenek a hanghoz.

### <a name="retrieve-activation-audio"></a>Aktiválási hang beolvasása

Hozzon létre egy [AudioGraph](/uwp/api/windows.media.audio.audiograph) , és adja át a következőnek: `CreateAudioDeviceInputNodeAsync` `ConversationalAgentSession` . Ezzel betölti a gráf hangpufferét, amely *körülbelül 3 másodpercet vesz igénybe, mielőtt a rendszer észlelte a kulcsszót*. Ez a további vezető hang a kulcsszó hosszának és a beszélő sebességének széles választékát tartalmazza. Ezt követően az [QuantumStarted](/uwp/api/windows.media.audio.audiograph.quantumstarted) eseményt a hanggráfból kezelheti a hangadatok lekéréséhez.

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

Megjegyzés: az audio pufferben található vezető hang a kulcsszó-ellenőrzés sikertelenségét okozhatja. A probléma megoldásához vágja le a hangpuffer elejét, mielőtt elküldi a hangot a kulcsszó-ellenőrzéshez. Ezt a kezdeti körülvágást az egyes segédekhez kell igazítani.

### <a name="launch-in-the-foreground"></a>Indítás az előtérben

Ha a kulcsszó ellenőrzése sikeres, az alkalmazásnak az előtérben kell áttérnie a felhasználói felület megjelenítéséhez. Hívja `ConversationalAgentSession.RequestForegroundActivationAsync` meg az API-t az alkalmazás előtérben való áthelyezéséhez.

### <a name="transition-from-compact-view-to-full-view"></a>Váltás a tömör nézetből a teljes nézetbe

Ha az alkalmazást a hang első aktiválása után aktiválja, a rendszer egy kompakt nézetben indítja el. A hangvezérelt [aktiválás előzetes kialakításával](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) kapcsolatos útmutatásért olvassa el a Windows rendszerű hangasszisztensek közötti különböző nézeteket és átmeneteket.

Az ApplicationView API-val a kompakt nézetből teljes alkalmazás nézetre való áttérést végezheti el `TryEnterViewModeAsync` :

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>A fenti zárolás aktiválásának megvalósítása

Az alábbi lépések azokat a követelményeket ismertetik, amelyek lehetővé teszik, hogy a Windows hangasszisztense a fenti zárolást futtassa, beleértve az alkalmazások életciklusának kezeléséhez szükséges példákra mutató hivatkozásokat is.

A zárolási tapasztalatok megtervezésével kapcsolatos útmutatásért tekintse meg az [ajánlott eljárásokat ismertető útmutatót](windows-voice-assistants-best-practices.md).

Ha egy alkalmazás a zárolás fölött egy nézetet jelenít meg, akkor azt "kioszk módban" tekinti. A kioszk üzemmódot használó alkalmazások megvalósításával kapcsolatos további információkért tekintse meg a [kioszk mód dokumentációját](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access).

### <a name="transitioning-above-lock"></a>Áttérés a fenti zárolás felett

A zárolás fenti aktiválása hasonló a zárolás alatti aktiváláshoz. Ha az alkalmazás nem rendelkezik aktív példányokkal, a háttérben egy új példány lesz elindítva, és a `OnBackgroundActivated` rendszer a app.XAML.cs fogja hívni. Ha az alkalmazás egy példánya van, akkor ez a példány értesítést kap az `ConversationalAgentSession.SignalDetected` eseményen.

Ha az alkalmazás már nem jelenik meg a zárolás felett, meg kell hívnia a következőt: `ConversationalAgentSession.RequestForegroundActivationAsync` . Ez elindítja a `OnLaunched` metódust a app.XAML.cs-ben, amely a zárolást megelőzően megjelenítendő nézetre fog navigálni.

### <a name="detecting-lock-screen-transitions"></a>Zárolási képernyő-átmenetek észlelése

A Windows SDK ConversationalAgent könyvtára olyan API-t biztosít, amely lehetővé teszi a zárolási képernyő állapotának és a zárolási képernyő állapotának módosítását könnyen elérhetővé. A zárolási képernyő aktuális állapotának észleléséhez ellenőrizze a `ConversationalAgentSession.IsUserAuthenticated` mezőt. A zárolási állapot változásainak észleléséhez adjon hozzá egy eseménykezelőt az `ConversationalAgentSession` objektum `SystemStateChanged` eseményéhez. Akkor következik be, amikor a képernyő zárolása zárolva értékre változik, vagy fordítva. Ha az esemény argumentumok értéke `ConversationalAgentSystemStateChangeType.UserAuthentication` , akkor a zárolási képernyő állapota megváltozott.

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>A zárolási aktiválás felhasználói preferenciájának észlelése

A Hangaktiválás adatvédelmi beállításai oldalon található alkalmazás bejegyzése a zárolási funkciók fenti funkcióit mutató kapcsolóval rendelkezik. Ahhoz, hogy az alkalmazás el tudja indítani a zárolást, a felhasználónak be kell kapcsolnia ezt a beállítást. A zárolási engedélyek fenti állapotát a ActivationSignalDetectionConfiguration objektum is tárolja. A AvailabilityInfo. HasLockScreenPermission állapota azt jelzi, hogy a felhasználó a zárolási engedélyt adta-e meg. Ha ez a beállítás le van tiltva, a hangalkalmazás arra kéri a felhasználót, hogy navigáljon a megfelelő beállítások lapra az "MS-Settings: Privacy-voiceactivation" hivatkozásnál, amely útmutatást nyújt az alkalmazás fenti zárolási aktiválásának engedélyezéséhez.

## <a name="closing-the-application"></a>Az alkalmazás bezárása

Ha programozott módon szeretné bezárni az alkalmazást a zárolás felett vagy alatt, használja az `WindowService.CloseWindow()` API-t. Ez elindítja az összes UWP életciklus-módszert, beleértve a OnSuspend is, amelyek lehetővé teszik az alkalmazás számára, hogy a lezárás előtt ártalmatlanítsa a `ConversationalAgentSession` példányát.

> [!NOTE]
> Az alkalmazás bezárható az [alábbi zárolási példány](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-)bezárása nélkül. Ebben az esetben a fenti zárolási nézetet a "tisztítás" értékre kell állítani, így biztosítva, hogy a képernyő zárolása után ne kelljen a fenti zárolási nézetet módosítani.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tekintse meg a példákat és a kódot bemutató UWP hangsegéd minta alkalmazást](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
