---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: 7ff53a700ab1b158d94af84907940f56afa74eb5
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99213808"
---
Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Windows beszédfelismerési eszközökhöz készült SDK-t egy beszédfelismerésre alkalmas termék létrehozásához vagy [beszélgetéses átírási](../conversation-transcription.md) eszközként való használatához. A beszélgetések átírásához csak az [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) támogatott. Más beszédekhez a mikrofonos tömb geometriáját biztosító lineáris MIC-tömbök támogatottak.

Az alkalmazás a Speech SDK csomaggal, valamint az Eclipse Java IDE (v4) a 64 bites Windows rendszeren készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

Ehhez az útmutatóhoz egy Speech Service-erőforrással rendelkező [Azure Cognitive Services](../overview.md#try-the-speech-service-for-free) -fiókra van szükség.

A [minta alkalmazás](https://aka.ms/sdsdk-download-JRE) forráskódját a Speech Devices SDK tartalmazza. [A githubon is elérhető](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Operációs rendszer: 64 bites Windows
* Egy mikrofonos tömb, például az [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* Csak [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) vagy [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) esetén.
* [Microsoft Visual C++ újraterjeszthető csomag](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be az egyiket ingyenesen](../overview.md#try-the-speech-service-for-free).
* Töltse le a Javához készült [Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) legújabb verzióját, és bontsa ki a. zip fájlt a munkakönyvtárba.
   > [!NOTE]
   > Ez a rövid útmutató azt feltételezi, hogy az alkalmazást a rendszer kinyeri a C:\SDSDK\JRE-Sample-Release

A beszélgetés átírása jelenleg csak az "en-US" és a "zh-CN" esetében érhető el az "CentralUS" és a "eastasia" régióban. A beszélgetés átírásának használatához az egyik régióban kell lennie egy beszédfelismerési kulcsnak.

Ha azt tervezi, hogy használja a leképezéseket, szüksége lesz egy [Language Understanding Service (Luis)](../../luis/luis-how-to-azure-subscription.md) előfizetésre. Ha többet szeretne megtudni a LUIS és a szándék-felismerésről, tekintse meg a következőt: [beszédfelismerési szándékok felismerése Luis-vel, C#](../how-to-recognize-intents-from-speech-csharp.md). Ehhez az alkalmazáshoz egy [minta Luis-modell](https://aka.ms/sdsdk-luis) érhető el.

## <a name="create-and-configure-the-project"></a>A projekt létrehozása és konfigurálása

1. Indítsa el az Eclipse-et.

1. Az **Eclipse ide-indító** **munkaterület** mezőjébe írja be az új munkaterület-könyvtár nevét. Ezután válassza a **Launch** (Indítás) lehetőséget.

   ![Képernyőkép, amely megjeleníti az Eclipse-indítót, ahol megadhatja a munkaterület könyvtárának nevét.](../media/speech-devices-sdk/eclipse-launcher.png)

1. Az Eclipse IDE főablaka hamarosan megjelenik. Ha megnyílt, zárja be az üdvözlőképernyőt.

1. Az Eclipse menüsávban hozzon létre egy új projektet a **fájl**  >  **új**  >  **Java-projekt** lehetőség kiválasztásával. Ha nem érhető el, válassza a **projekt** , majd a **Java-projekt** lehetőséget.

1. Elindul az **új Java-projekt** varázsló. **Tallózással keresse** meg a minta projekt helyét. Válassza a **Befejezés** gombot.

   ![Az új Java-projekt varázslót bemutató képernyőkép.](../media/speech-devices-sdk/eclipse-new-java-project.png)

1. A **Package Explorerben** kattintson a jobb gombbal a projektre. Válassza   >  a helyi menü **Konvertálás a Maven-re projektre** parancsát. Válassza a **Befejezés** gombot.

   ![A Package Explorer képernyőképe](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Nyissa meg és szerkessze a pom.xml fájlt.

    A fájl végén, a záró címke `</project>` , a létrehozás `repositories` és `dependencies` az elemek előtt az itt látható módon, és ellenőrizze, hogy az megfelel-e az `version` aktuális verziónak:
    ```xml
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.15.0</version>
        </dependency>
    </dependencies>
   ```

1. Másolja a **Windows-x64** tartalmat a Java-projekt helyére, például **C:\SDSDK\JRE-Sample-Release**

1. Másolás `kws.table` `participants.properties` és `Microsoft.CognitiveServices.Speech.extension.pma.dll` a projekt mappájába **target\classes**

## <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

1. Adja hozzá a beszédfelismerési előfizetéshez tartozó kulcsot a forráskódhoz. Ha szeretné kipróbálni a szándék felismerését, adja hozzá a [Language Understanding szolgáltatás](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) előfizetési kulcsát és az alkalmazás azonosítóját is.

   A Speech and LUIS esetében az adatai a következőre mutatnak `FunctionsList.java` :

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Ha a társalgási átiratot használja, a beszédfelismerési kulcsra és a régióra vonatkozó információkra is szükség van a következőkben `Cts.java` :

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Az alapértelmezett kulcsszó (kulcsszó) a "Computer". Kipróbálhatja a többi megadott kulcsszót is, például a "Machine" vagy a "Assistant" kifejezést. Ezen alternatív kulcsszavak erőforrásai a Speech Devices SDK-ban, a Kulcsszóválasztó mappában találhatók. Például `C:\SDSDK\JRE-Sample-Release\keyword\Computer` a "számítógép" kulcsszóhoz használt fájlokat tartalmazza.

    > [!TIP]
    > [Egyéni kulcsszó is létrehozható](../custom-keyword-basics.md).

    Új kulcsszó használatához frissítse a következő sort a-ben `FunctionsList.java` , és másolja a kulcsszót az alkalmazásba. Ha például a "Machine" kulcsszót szeretné használni a Kulcsszóválasztó csomagból `machine.zip` :

   * Másolja a `kws.table` fájlt a zip-csomagból a Project Folder **cél/osztályok** mappájába.
   * Frissítse a `FunctionsList.java` kulcsszó nevét a következővel:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>A minta alkalmazás futtatása az Eclipse-ből

1. Az Eclipse menüsávban futtassa a   >  **Run as**  >  **Java-alkalmazást**. Ezután válassza a **FunctionsList** és **az OK gombot**.

   ![A Java-alkalmazás kiválasztása – képernyőfelvétel](../media/speech-devices-sdk/eclipse-run-sample.png)

1. Elindul a Speech Devices SDK példa alkalmazás, amely a következő lehetőségeket jeleníti meg:

   ![Képernyőkép egy példa Speech Devices SDK-alkalmazásról és-lehetőségekről.](../media/speech-devices-sdk/java-sample-app-windows.png)

1. Próbálja ki az új **beszélgetés átiratának** bemutatóját. Kezdje el az átírást a **munkamenet**  >  **elindításával**. Alapértelmezés szerint mindenki a vendég. Ha azonban a résztvevő hangaláírásai vannak, akkor `participants.properties` a projekt mappájában a **cél/osztályok** fájlba helyezhetők. A hangaláírás létrehozásához tekintse meg a beszélgetések átírása [(SDK) című témakört](../how-to-use-conversation-transcription.md).

   ![Képernyőkép a bemutató beszélgetés átiratának alkalmazásáról.](../media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Önálló alkalmazás létrehozása és futtatása

1. A **Package Explorerben** kattintson a jobb gombbal a projektre. Válassza az **Exportálás** lehetőséget.

1. Megjelenik az **Exportálás** ablak. Bontsa ki a **javát** , és válassza a **futtatható jar-fájl** lehetőséget, majd kattintson a **tovább** gombra.

   ![Képernyőfelvétel: az Exportálás ablak, ahol kiválasztja a futtatható JAR-fájlt.](../media/speech-devices-sdk/eclipse-export-windows.png)

1. Megjelenik a **FUTTATHATÓ jar-fájl exportálása** ablak. Válassza ki az alkalmazás **exportálási célját** , majd kattintson a **Befejezés gombra**.

   ![Képernyőkép, amely megjeleníti a futtatható JAR fájl exportálási ablakát, ahol az Exportálás célhelyét választja.](../media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. A `kws.table` `participants.properties` `unimic_runtime.dll` `pma.dll` fent kiválasztott célmappában helyezze el a,,, és `Microsoft.CognitiveServices.Speech.extension.pma.dll` a fájlt, mivel az alkalmazásnak szüksége van rájuk.

1. Az önálló alkalmazás futtatása

   ```powershell
   java -jar SpeechDemo.jar
   ```