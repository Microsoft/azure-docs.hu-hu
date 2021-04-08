---
title: Hangbemeneti eszköz kiválasztása a Speech SDK-val
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan lehet hangbeviteli eszközöket kiválasztani a Speech SDK-ban (C++, C#, Python, Objective-C, Java, JavaScript) a rendszerhez csatlakoztatott hangeszközök azonosítóinak beszerzésével.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: chlandsi
ms.custom: devx-track-js
ms.openlocfilehash: 48316d571eac835dd5d4ec7d225048f4fdcdf237
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026607"
---
# <a name="how-to-select-an-audio-input-device-with-the-speech-sdk"></a>Útmutató: hangbemeneti eszköz kiválasztása a Speech SDK-val

A Speech SDK 1.3.0 verziója bevezet egy API-t a hangbemenet kiválasztásához. Ez a cikk a rendszerhez csatlakoztatott hangeszközök azonosítóinak beszerzését ismerteti. Ezek ezután a Speech SDK-ban használhatók a hangeszköz objektumon keresztüli konfigurálásával `AudioConfig` :

```C++
audioConfig = AudioConfig.FromMicrophoneInput("<device id>");
```

```cs
audioConfig = AudioConfig.FromMicrophoneInput("<device id>");
```

```Python
audio_config = AudioConfig(device_name="<device id>");
```

```objc
audioConfig = AudioConfiguration.FromMicrophoneInput("<device id>");
```

```Java
audioConfig = AudioConfiguration.fromMicrophoneInput("<device id>");
```

```JavaScript
audioConfig = AudioConfiguration.fromMicrophoneInput("<device id>");
```

> [!Note]
> A mikrofon használata nem érhető el a Node.jsban futó JavaScriptek esetében

## <a name="audio-device-ids-on-windows-for-desktop-applications"></a>Hangeszközök azonosítói Windows asztali alkalmazásokhoz

Az eszközök [végpont-azonosító sztringje](/windows/desktop/CoreAudio/endpoint-id-strings) a [`IMMDevice`](/windows/desktop/api/mmdeviceapi/nn-mmdeviceapi-immdevice) Windows asztali alkalmazásokban található objektumból kérhető le.

A következő mintakód azt szemlélteti, hogyan használható a C++ hangeszközök enumerálásához:

```cpp
#include <cstdio>
#include <mmdeviceapi.h>

#include <Functiondiscoverykeys_devpkey.h>

const CLSID CLSID_MMDeviceEnumerator = __uuidof(MMDeviceEnumerator);
const IID IID_IMMDeviceEnumerator = __uuidof(IMMDeviceEnumerator);

constexpr auto REFTIMES_PER_SEC = (10000000 * 25);
constexpr auto REFTIMES_PER_MILLISEC = 10000;

#define EXIT_ON_ERROR(hres)  \
              if (FAILED(hres)) { goto Exit; }
#define SAFE_RELEASE(punk)  \
              if ((punk) != NULL)  \
                { (punk)->Release(); (punk) = NULL; }

void ListEndpoints();

int main()
{
    CoInitializeEx(NULL, COINIT_MULTITHREADED);
    ListEndpoints();
}

//-----------------------------------------------------------
// This function enumerates all active (plugged in) audio
// rendering endpoint devices. It prints the friendly name
// and endpoint ID string of each endpoint device.
//-----------------------------------------------------------
void ListEndpoints()
{
    HRESULT hr = S_OK;
    IMMDeviceEnumerator *pEnumerator = NULL;
    IMMDeviceCollection *pCollection = NULL;
    IMMDevice *pEndpoint = NULL;
    IPropertyStore *pProps = NULL;
    LPWSTR pwszID = NULL;

    hr = CoCreateInstance(CLSID_MMDeviceEnumerator, NULL, CLSCTX_ALL, IID_IMMDeviceEnumerator, (void**)&pEnumerator);
    EXIT_ON_ERROR(hr);

    hr = pEnumerator->EnumAudioEndpoints(eCapture, DEVICE_STATE_ACTIVE, &pCollection);
    EXIT_ON_ERROR(hr);

    UINT  count;
    hr = pCollection->GetCount(&count);
    EXIT_ON_ERROR(hr);

    if (count == 0)
    {
        printf("No endpoints found.\n");
    }

    // Each iteration prints the name of an endpoint device.
    PROPVARIANT varName;
    for (ULONG i = 0; i < count; i++)
    {
        // Get pointer to endpoint number i.
        hr = pCollection->Item(i, &pEndpoint);
        EXIT_ON_ERROR(hr);

        // Get the endpoint ID string.
        hr = pEndpoint->GetId(&pwszID);
        EXIT_ON_ERROR(hr);

        hr = pEndpoint->OpenPropertyStore(
            STGM_READ, &pProps);
        EXIT_ON_ERROR(hr);

        // Initialize container for property value.
        PropVariantInit(&varName);

        // Get the endpoint's friendly-name property.
        hr = pProps->GetValue(PKEY_Device_FriendlyName, &varName);
        EXIT_ON_ERROR(hr);

        // Print endpoint friendly name and endpoint ID.
        printf("Endpoint %d: \"%S\" (%S)\n", i, varName.pwszVal, pwszID);

        CoTaskMemFree(pwszID);
        pwszID = NULL;
        PropVariantClear(&varName);
    }

Exit:
    CoTaskMemFree(pwszID);
    pwszID = NULL;
    PropVariantClear(&varName);
    SAFE_RELEASE(pEnumerator);
    SAFE_RELEASE(pCollection);
    SAFE_RELEASE(pEndpoint);
    SAFE_RELEASE(pProps);
}
```

A C# nyelvben a [NAudio](https://github.com/naudio/NAudio) függvénytár a CoreAudio API eléréséhez és az eszközök enumerálásához használható a következő módon:

```cs
using System;

using NAudio.CoreAudioApi;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            var enumerator = new MMDeviceEnumerator();
            foreach (var endpoint in
                     enumerator.EnumerateAudioEndPoints(DataFlow.Capture, DeviceState.Active))
            {
                Console.WriteLine("{0} ({1})", endpoint.FriendlyName, endpoint.ID);
            }
        }
    }
}
```

A minta eszköz azonosítója: `{0.0.1.00000000}.{5f23ab69-6181-4f4a-81a4-45414013aac8}` .

## <a name="audio-device-ids-on-uwp"></a>Hangeszközök azonosítói a UWP

A Univerzális Windows-platform (UWP) hangbeviteli eszközöket a `Id()` megfelelő objektum tulajdonságának használatával lehet beszerezni [`DeviceInformation`](/uwp/api/windows.devices.enumeration.deviceinformation) .

A következő mintakód bemutatja, hogyan végezheti el ezt a C++ és a C# nyelvben:

```cpp
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Devices.Enumeration.h>

using namespace winrt::Windows::Devices::Enumeration;

void enumerateDeviceIds()
{
    auto promise = DeviceInformation::FindAllAsync(DeviceClass::AudioCapture);

    promise.Completed(
        [](winrt::Windows::Foundation::IAsyncOperation<DeviceInformationCollection> const& sender,
           winrt::Windows::Foundation::AsyncStatus /* asyncStatus */ ) {
        auto info = sender.GetResults();
        auto num_devices = info.Size();

        for (const auto &device : info)
        {
            std::wstringstream ss{};
            ss << "looking at device (of " << num_devices << "): " << device.Id().c_str() << "\n";
            OutputDebugString(ss.str().c_str());
        }
    });
}
```

```cs
using Windows.Devices.Enumeration;
using System.Linq;

namespace helloworld {
    private async void EnumerateDevices()
    {
        var devices = await DeviceInformation.FindAllAsync(DeviceClass.AudioCapture);

        foreach (var device in devices)
        {
            Console.WriteLine($"{device.Name}, {device.Id}\n");
        }
    }
}
```

A minta eszköz azonosítója: `\\\\?\\SWD#MMDEVAPI#{0.0.1.00000000}.{5f23ab69-6181-4f4a-81a4-45414013aac8}#{2eef81be-33fa-4800-9670-1cd474972c3f}` .

## <a name="audio-device-ids-on-linux"></a>Audio-eszközök azonosítói Linuxon

Az eszközök azonosítói a szabványos ALSA-eszközök azonosítói alapján vannak kiválasztva.

A rendszerhez csatolt bemenetek azonosítói a parancs kimenetében találhatók `arecord -L` .
Azt is megteheti, hogy az [ALSA C könyvtár](https://www.alsa-project.org/alsa-doc/alsa-lib/)használatával szerzi be őket.

A minták azonosítói a következők: `hw:1,0` és `hw:CARD=CC,DEV=0` .

## <a name="audio-device-ids-on-macos"></a>Hangeszközök azonosítói macOS rendszeren

Az Objective-C-ben implementált következő függvény a Mac számítógéphez csatlakoztatott hangeszközök neveinek és azonosítóinak listáját hozza létre.

A `deviceUID` karakterlánc a MacOS-hez készült SPEECH SDK-ban található eszköz azonosítására szolgál.

```objc
#import <Foundation/Foundation.h>
#import <CoreAudio/CoreAudio.h>

CFArrayRef CreateInputDeviceArray()
{
    AudioObjectPropertyAddress propertyAddress = {
        kAudioHardwarePropertyDevices,
        kAudioObjectPropertyScopeGlobal,
        kAudioObjectPropertyElementMaster
    };

    UInt32 dataSize = 0;
    OSStatus status = AudioObjectGetPropertyDataSize(kAudioObjectSystemObject, &propertyAddress, 0, NULL, &dataSize);
    if (kAudioHardwareNoError != status) {
        fprintf(stderr, "AudioObjectGetPropertyDataSize (kAudioHardwarePropertyDevices) failed: %i\n", status);
        return NULL;
    }

    UInt32 deviceCount = (uint32)(dataSize / sizeof(AudioDeviceID));

    AudioDeviceID *audioDevices = (AudioDeviceID *)(malloc(dataSize));
    if (NULL == audioDevices) {
        fputs("Unable to allocate memory", stderr);
        return NULL;
    }

    status = AudioObjectGetPropertyData(kAudioObjectSystemObject, &propertyAddress, 0, NULL, &dataSize, audioDevices);
    if (kAudioHardwareNoError != status) {
        fprintf(stderr, "AudioObjectGetPropertyData (kAudioHardwarePropertyDevices) failed: %i\n", status);
        free(audioDevices);
        audioDevices = NULL;
        return NULL;
    }

    CFMutableArrayRef inputDeviceArray = CFArrayCreateMutable(kCFAllocatorDefault, deviceCount, &kCFTypeArrayCallBacks);
    if (NULL == inputDeviceArray) {
        fputs("CFArrayCreateMutable failed", stderr);
        free(audioDevices);
        audioDevices = NULL;
        return NULL;
    }

    // Iterate through all the devices and determine which are input-capable
    propertyAddress.mScope = kAudioDevicePropertyScopeInput;
    for (UInt32 i = 0; i < deviceCount; ++i) {
        // Query device UID
        CFStringRef deviceUID = NULL;
        dataSize = sizeof(deviceUID);
        propertyAddress.mSelector = kAudioDevicePropertyDeviceUID;
        status = AudioObjectGetPropertyData(audioDevices[i], &propertyAddress, 0, NULL, &dataSize, &deviceUID);
        if (kAudioHardwareNoError != status) {
            fprintf(stderr, "AudioObjectGetPropertyData (kAudioDevicePropertyDeviceUID) failed: %i\n", status);
            continue;
        }

        // Query device name
        CFStringRef deviceName = NULL;
        dataSize = sizeof(deviceName);
        propertyAddress.mSelector = kAudioDevicePropertyDeviceNameCFString;
        status = AudioObjectGetPropertyData(audioDevices[i], &propertyAddress, 0, NULL, &dataSize, &deviceName);
        if (kAudioHardwareNoError != status) {
            fprintf(stderr, "AudioObjectGetPropertyData (kAudioDevicePropertyDeviceNameCFString) failed: %i\n", status);
            continue;
        }

        // Determine if the device is an input device (it is an input device if it has input channels)
        dataSize = 0;
        propertyAddress.mSelector = kAudioDevicePropertyStreamConfiguration;
        status = AudioObjectGetPropertyDataSize(audioDevices[i], &propertyAddress, 0, NULL, &dataSize);
        if (kAudioHardwareNoError != status) {
            fprintf(stderr, "AudioObjectGetPropertyDataSize (kAudioDevicePropertyStreamConfiguration) failed: %i\n", status);
            continue;
        }

        AudioBufferList *bufferList = (AudioBufferList *)(malloc(dataSize));
        if (NULL == bufferList) {
            fputs("Unable to allocate memory", stderr);
            break;
        }

        status = AudioObjectGetPropertyData(audioDevices[i], &propertyAddress, 0, NULL, &dataSize, bufferList);
        if (kAudioHardwareNoError != status || 0 == bufferList->mNumberBuffers) {
            if (kAudioHardwareNoError != status)
                fprintf(stderr, "AudioObjectGetPropertyData (kAudioDevicePropertyStreamConfiguration) failed: %i\n", status);
            free(bufferList);
            bufferList = NULL;
            continue;
        }

        free(bufferList);
        bufferList = NULL;

        // Add a dictionary for this device to the array of input devices
        CFStringRef keys    []  = { CFSTR("deviceUID"),     CFSTR("deviceName")};
        CFStringRef values  []  = { deviceUID,              deviceName};

        CFDictionaryRef deviceDictionary = CFDictionaryCreate(kCFAllocatorDefault,
                                                              (const void **)(keys),
                                                              (const void **)(values),
                                                              2,
                                                              &kCFTypeDictionaryKeyCallBacks,
                                                              &kCFTypeDictionaryValueCallBacks);

        CFArrayAppendValue(inputDeviceArray, deviceDictionary);

        CFRelease(deviceDictionary);
        deviceDictionary = NULL;
    }

    free(audioDevices);
    audioDevices = NULL;

    // Return a non-mutable copy of the array
    CFArrayRef immutableInputDeviceArray = CFArrayCreateCopy(kCFAllocatorDefault, inputDeviceArray);
    CFRelease(inputDeviceArray);
    inputDeviceArray = NULL;

    return immutableInputDeviceArray;
}
```

A beépített mikrofonhoz tartozó UID például a következő: `BuiltInMicrophoneDevice` .

## <a name="audio-device-ids-on-ios"></a>Hangeszközök azonosítói az iOS-en

A Speech SDK-val a hangeszköz kiválasztása nem támogatott iOS rendszeren. Az SDK-t használó alkalmazások azonban befolyásolhatják a keretrendszeren keresztüli hang-útválasztást [`AVAudioSession`](https://developer.apple.com/documentation/avfoundation/avaudiosession?language=objc) .

Például az utasítás

```objc
[[AVAudioSession sharedInstance] setCategory:AVAudioSessionCategoryRecord
    withOptions:AVAudioSessionCategoryOptionAllowBluetooth error:NULL];
```

engedélyezi a Bluetooth-fejhallgató használatát egy beszédfelismerést támogató alkalmazáshoz.

## <a name="audio-device-ids-in-javascript"></a>Hangeszközök azonosítói a JavaScriptben

A JavaScriptben a [MediaDevices. enumerateDevices ()](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/enumerateDevices) metódus használatával enumerálhatja az adathordozó eszközöket, és megkeresheti a továbbítani kívánt eszköz azonosítóját `fromMicrophone(...)` .

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A GitHubon található minták megismerése](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Akusztikai modellek testreszabása](./how-to-custom-speech-train-model.md)
- [Nyelvi modellek testreszabása](./how-to-custom-speech-train-model.md)