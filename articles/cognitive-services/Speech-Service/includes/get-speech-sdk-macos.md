---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 82c45919892721d689bd90b7480158c4eea16c03
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87375001"
---
A macOS-re való fejlesztéskor három beszédfelismerési SDK érhető el.

- A Objective-C Speech SDK natív módon CocoaPod-csomagként érhető el
- A .NET Speech SDK a **Xamarin. Mac** használatával használható, mivel a .net Standard 2,0-es implementációja
- A Python Speech SDK PyPI modulként érhető el

> [!TIP]
> A Swift Objective-C Speech SDK használatával kapcsolatos részletekért lásd: az <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Objective-c importálása Swift <span class="docon docon-navigate-external x-hidden-focus"></span> -be </a>.

### <a name="system-requirements"></a>Rendszerkövetelmények

- MacOS 10,13-es vagy újabb verzió

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        A macOS CocoaPod csomag letölthető és használható a <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (vagy újabb) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> integrált fejlesztési környezettel (ide). Először <a href="https://aka.ms/csspeech/macosbinary" target="_blank">töltse le a bináris CocoaPod <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Bontsa ki a pod-t ugyanabban a címtárban a kívánt használatra, hozzon létre egy *cocoapods* , és sorolja fel a következőt: `pod` `target` .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.13.0'
end
```

# <a name="xamarinmac"></a>[Xamarin. Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        A Xamarin. Mac a teljes macOS SDK for .NET-fejlesztők számára teszi lehetővé natív Mac-alkalmazások kiépítését a C# használatával. További információ: <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin. Mac <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>További források

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS Speech SDK gyors üzembe helyezési célkitűzés – C forráskód<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS Speech SDK gyors útmutató Swift-forráskódja<span class="docon docon-navigate-external x-hidden-focus"></span></a>