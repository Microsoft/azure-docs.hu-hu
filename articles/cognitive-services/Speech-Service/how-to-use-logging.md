---
title: Beszédfelismerési SDK naplózása – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan engedélyezheti a naplózást a Speech SDK-ban (C++, C#, Python, Objective-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: f044596cf084e68b6c9ca10e3da4fbdf4c8c2062
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026522"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Naplózás engedélyezése a Speech SDK-ban

A fájlba való naplózás a Speech SDK választható funkciója. A fejlesztési naplózás során további információkat és diagnosztikát biztosít a Speech SDK alapvető összetevőiből. Ezt úgy engedélyezheti, `Speech_LogFilename` Ha egy beszéd konfigurációs objektum tulajdonságát a naplófájl helyére és nevére állítja be. A naplózást a rendszer globálisan aktiválja, ha a rendszer létrehoz egy felismerőt ebből a konfigurációból, és ezt követően nem lehet letiltani. A naplófájlok neve nem módosítható egy futó naplózási munkamenet során.

> [!NOTE]
> A naplózás elérhető a Speech SDK verziójának 1.4.0 az összes támogatott Speech SDK programozási nyelvben, a JavaScript kivételével.

## <a name="sample"></a>Sample

A naplófájl neve egy konfigurációs objektumon van megadva. `SpeechConfig`Tekintse át a példát, és feltételezve, hogy létrehozott egy nevű példányt `config` :

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

A konfigurációs objektumból létrehozhat egy felismerőt. Ez lehetővé teszi az összes felismerő naplózását.

> [!NOTE]
> Ha `SpeechSynthesizer` a-t a konfigurációs objektumból hozza létre, az nem teszi lehetővé a naplózást. Ha a naplózás engedélyezve van, akkor a-től is megkapja a diagnosztikát `SpeechSynthesizer` .

## <a name="create-a-log-file-on-different-platforms"></a>Naplófájl létrehozása különböző platformokon

Windows vagy Linux esetén a naplófájl bármilyen elérési úton lehet, amelyhez a felhasználó rendelkezik írási engedéllyel. Az írási engedélyek más operációs rendszerekben a fájlrendszer helyeire korlátozhatók vagy alapértelmezés szerint korlátozottak lehetnek.

### <a name="universal-windows-platform-uwp"></a>Univerzális Windows-platform (UWP)

A UWP-alkalmazásoknak naplófájlokat kell megadniuk az alkalmazásadatok egyik helyén (helyi, barangoló vagy ideiglenes). Naplófájl hozható létre a helyi alkalmazás mappájában:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

További információ a UWP-alkalmazásokkal kapcsolatos fájl-hozzáférési engedélyekről [itt](/windows/uwp/files/file-access-permissions)érhető el.

### <a name="android"></a>Android

A naplófájlok menthetők a belső tárolóba, a külső tárolóba vagy a gyorsítótár könyvtárába. A belső tárolóban vagy a gyorsítótár könyvtárában létrehozott fájlok magánjellegűek az alkalmazás számára. Érdemes lehet naplófájlt létrehozni a külső tárolóban.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

A fenti kód egy alkalmazásspecifikus könyvtár gyökerében lévő külső tárolóba menti a naplófájlt. A felhasználók a fájlkezelővel (általában a verzióban) férhetnek hozzá a fájlhoz `Android/data/ApplicationName/logfile.txt` . A rendszer törli a fájlt az alkalmazás eltávolításakor.

`WRITE_EXTERNAL_STORAGE`A jegyzékfájlban is engedélyt kell kérnie:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Az Android-alkalmazások adatainak és file Storage szolgáltatásával kapcsolatos további információk [itt](https://developer.android.com/guide/topics/data/data-storage.html)érhetők el.

#### <a name="ios"></a>iOS

Csak az alkalmazás-Sandboxon belüli könyvtárak érhetők el. A fájlok a dokumentumok, a könyvtár és a temp könyvtárakban hozhatók létre. A dokumentumok könyvtárban lévő fájlok elérhetővé tehetők a felhasználók számára. A következő kódrészlet a naplófájlok létrehozását mutatja be az alkalmazási dokumentum könyvtárába:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Egy létrehozott fájl eléréséhez adja hozzá az alábbi tulajdonságokat az `Info.plist` alkalmazás tulajdonságainak listájához:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

További információ az iOS fájlrendszerről [itt](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)érhető el.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A GitHubon található minták megismerése](https://aka.ms/csspeech/samples)