---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: 3b473d82eb879955a6cbdce58ba66176f5c6d6f5
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99213805"
---
Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Androidhoz készült Speech Devices SDK-t, hogy beszédfelismerésre alkalmas terméket hozzon létre, vagy [beszélgetéses átírási](../conversation-transcription.md) eszközként használja azt.

Ehhez az útmutatóhoz egy Speech Service-erőforrással rendelkező [Azure Cognitive Services](../overview.md#try-the-speech-service-for-free) -fiókra van szükség.

A minta alkalmazás forráskódját a Speech Devices SDK tartalmazza. [A githubon is elérhető](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Előfeltételek

A Speech Devices SDK használatának megkezdése előtt a következőket kell tennie:

- Kövesse a [fejlesztői csomag](../get-speech-devices-sdk.md) utasításait az eszköz bekapcsolásához.

- Töltse le a [Speech Devices SDK](../speech-devices-sdk.md)legújabb verzióját, és bontsa ki a. zip fájlt a munkakönyvtárba.

  > [!NOTE]
  > Ez a rövid útmutató azt feltételezi, hogy az alkalmazást a rendszer kinyeri a C:\SDSDK\Android-Sample-Release

- [Azure-előfizetési kulcs beszerzése a Speech Service-hez](../overview.md#try-the-speech-service-for-free)

- Ha azt tervezi, hogy a beszélgetés átírását használja, [körkörös mikrofonos eszközt](../get-speech-devices-sdk.md) kell használnia, és ez a funkció jelenleg csak az "en-us" és a "zh-CN" esetében érhető el a (z) "CentralUS" és a "eastasia" régióban. A beszélgetés átírásának használatához az egyik régióban kell lennie egy beszédfelismerési kulcsnak.

- Ha azt tervezi, hogy a Speech Service segítségével azonosítja a szándékokat (vagy műveleteket) a felhasználói hosszúságú kimondott szöveg, szüksége lesz egy [Language Understanding Service (Luis)](../../luis/luis-how-to-azure-subscription.md) előfizetésre. Ha többet szeretne megtudni a LUIS és a szándék-felismerésről, tekintse meg a következőt: [beszédfelismerési szándékok felismerése Luis-vel, C#](../how-to-recognize-intents-from-speech-csharp.md).

  [Létrehozhat egy egyszerű Luis-modellt](../../luis/index.yml) , vagy használhatja a (z) LUIS-example.jsLuis modellt is. A LUIS-modell a [Speech Devices SDK letöltési webhelyéről](https://aka.ms/sdsdk-luis)érhető el. A modell JSON-fájljának a Luis- [portálra](https://www.luis.ai/home)való feltöltéséhez válassza az **új alkalmazás importálása** lehetőséget, majd válassza ki a JSON-fájlt.

- Telepítse a [Android Studio](https://developer.android.com/studio/) és a [Vysor](https://vysor.io/download/) a számítógépre.

## <a name="set-up-the-device"></a>Az eszköz beállítása

1. Indítsa el a Vysor a számítógépen.

   ![Vysor](../media/speech-devices-sdk/qsg-3.png)

1. Az eszköznek az **eszköz kiválasztása** területen kell szerepelnie. Válassza a **nézet** gombot az eszköz mellett.

1. A vezeték nélküli hálózathoz való kapcsolódáshoz válassza a mappa ikont, majd a **Beállítások**  >  **WLAN** elemet.

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Ha a vállalata rendelkezik házirendekkel az eszközök Wi-Fi rendszerhez való csatlakoztatásával kapcsolatban, be kell szereznie a MAC-címeket, és kapcsolatba kell lépnie az informatikai részleggel arról, hogyan csatlakoztathatja azt a vállalati Wi-Fi-hez.
   >
   > A fejlesztői csomag MAC-címeinek megkereséséhez válassza ki a fájl mappa ikont a fejlesztői csomag asztalán.
   >
   > ![Vysor fájl mappája](../media/speech-devices-sdk/qsg-10.png)
   >
   > Válassza a **Beállítások** lehetőséget. Keressen rá a "MAC-címek" kifejezésre, majd válassza a **MAC-címek**  >  **speciális WLAN** lehetőséget. Jegyezze fel a párbeszédpanel alján megjelenő MAC-címeket.
   >
   > ![Vysor MAC-címe](../media/speech-devices-sdk/qsg-11.png)
   >
   > Előfordulhat, hogy egyes vállalatok számára az, hogy mennyi ideig lehet csatlakoztatni az eszközt a Wi-Fi rendszerhez. Előfordulhat, hogy egy adott számú nap elteltével ki kell terjesztenie a fejlesztői készlet regisztrációját a Wi-Fi rendszerbe.

## <a name="run-the-sample-application"></a>A mintaalkalmazás futtatása

A fejlesztői csomag telepítésének ellenőrzéséhez hozza létre és telepítse a minta alkalmazást:

1. Android Studio elindítása.

1. Válassza az **Open an existing Android Studio project** (Létező Android Studio-projekt megnyitása) lehetőséget.

   ![Android Studio – meglévő projekt megnyitása](../media/speech-devices-sdk/qsg-5.png)

1. Ugrás a C:\SDSDK\Android-Sample-Release\example. A példa projekt megnyitásához kattintson **az OK gombra** .

1. Konfigurálja a gradle a Speech SDK-ra való hivatkozáshoz. Az alábbi fájlok a Android Studio-ban található **Gradle-szkriptek** alatt találhatók.

    A **Build. gradle (projekt: példa)** frissítéséhez a allprojects blokknak a Maven-sorok hozzáadásával meg kell egyeznie az alábbi lépésekkel.

    ```xml
    allprojects {
        repositories {
            google()
            jcenter()
            mavenCentral()
            maven {
                url 'https://csspeechstorage.blob.core.windows.net/maven/'
            }
        }
    }
    ```

    A **Build. gradle (modul: alkalmazás)** frissítéséhez adja hozzá ezt a sort a függőségek szakaszhoz. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.15.0'
    ```
    
1. Adja hozzá a beszédfelismerési előfizetéshez tartozó kulcsot a forráskódhoz. Ha szeretné kipróbálni a szándék felismerését, adja hozzá a [Language Understanding szolgáltatás](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) előfizetési kulcsát és az alkalmazás azonosítóját is.

   A Speech and LUIS esetében az adatai a MainActivity. Java-ba kerülnek:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Ha a beszélgetési átiratot használja, a beszédfelismerési kulcs és a régió információi is szükségesek a beszélgetésben. Java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Az alapértelmezett kulcsszó a "Computer". Kipróbálhatja a többi megadott kulcsszót is, például a "Machine" vagy a "Assistant" kifejezést. Ezen alternatív kulcsszavak erőforrásai a Speech Devices SDK-ban, a Kulcsszóválasztó mappában találhatók. Például a C:\SDSDK\Android-Sample-Release\keyword\Computer tartalmazza a "Computer" kulcsszóhoz használt fájlokat.

   > [!TIP]
   > [Egyéni kulcsszó is létrehozható](../custom-keyword-basics.md).

   Új kulcsszó használatához frissítse a következő két sort a `MainActivity.java` alkalmazásban, és másolja a kulcsszó-csomagot az alkalmazásba. Ha például a "Machine" kulcsszót szeretné használni a Kulcsszóválasztó csomag kws-machine.zip:

   - Másolja a Kulcsszóválasztó csomagot a "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets" mappába \" .
   - Frissítse a `MainActivity.java` kulcsszót és a csomag nevét:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Frissítse a következő sorokat, amelyek tartalmazzák a mikrofon tömb geometriájának beállításait:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Ez a táblázat a támogatott értékeket sorolja fel:

   | Változó | Értelmezés | Elérhető értékek |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Fizikai MIC-konfiguráció | Körkörös fejlesztői csomag esetén: `Circular6+1` |
   |          |         | Lineáris fejlesztői csomag esetén: `Linear4` |
   | `SelectedGeometry` | Szoftveres MIC-konfiguráció | Az összes mikrofont használó körkörös fejlesztői csomag esetén: `Circular6+1` |
   |          |         | Négy mikrofont használó körkörös fejlesztői csomag esetén: `Circular3+1` |
   |          |         | Az összes mikrofont használó lineáris fejlesztői csomaghoz: `Linear4` |
   |          |         | Két mikrofont használó lineáris fejlesztői csomag esetén: `Linear2` |

1. Az alkalmazás létrehozásához a **Futtatás** menüben válassza az alkalmazás **futtatása** lehetőséget. Megjelenik a **központi telepítési cél kiválasztása** párbeszédpanel.

1. Válassza ki az eszközt, majd kattintson **az OK** gombra az alkalmazás telepítéséhez az eszközön.

   ![Telepítési cél kiválasztása párbeszédpanel](../media/speech-devices-sdk/qsg-7.png)

1. Elindul a Speech Devices SDK példa alkalmazás, amely a következő lehetőségeket jeleníti meg:

   ![Példa a beszédfelismerési eszközök SDK-alkalmazására és lehetőségeire](../media/speech-devices-sdk/qsg-8.png)

1. Próbálja ki az új beszélgetés átiratának bemutatóját. A "kezdési munkamenet" megkezdése. Alapértelmezés szerint mindenki a vendég. Ha azonban a résztvevő hang-aláírása is van, akkor az eszközön található fájlba helyezhetők `/video/participants.properties` . A hangaláírás létrehozásához tekintse meg a beszélgetések átírása [(SDK) című témakört](../how-to-use-conversation-transcription.md).

   ![Bemutató beszélgetés átirata alkalmazás](../media/speech-devices-sdk/qsg-15.png)

1. Kísérlet!

## <a name="troubleshooting"></a>Hibaelhárítás

Ha nem tud csatlakozni a beszédfelismerési eszközhöz. Írja be a következő parancsot egy parancssori ablakba. Az eszköz az eszközök listáját fogja visszaadni:

```powershell
 adb devices
```

> [!NOTE]
> Ez a parancs az androidos hibakeresési hidat használja, `adb.exe` amely a Android Studio telepítésének része. Ez az eszköz a C:\Users \[ Felhasználónév] \AppData\Local\Android\Sdk\platform-Tools. található. Ezt a könyvtárat hozzáadhatja az elérési úthoz, hogy kényelmesebb legyen a meghívása `adb` . Ellenkező esetben meg kell adnia a adb.exe telepítésének teljes elérési útját minden meghívott parancsban `adb` .
>
> Ha hibaüzenet jelenik meg, `no devices/emulators found` ellenőrizze, hogy az USB-kábel csatlakoztatva van-e, és hogy a rendszer kiváló minőségű kábelt használ-e.