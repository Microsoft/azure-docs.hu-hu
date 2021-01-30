---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: travisw
ms.openlocfilehash: b987f98281c298da2d634c686d740faf3dda3502
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214701"
---
1. Indítsa el Android Studio, majd válassza az **új Android Studio projekt indítása** lehetőséget az **üdvözlő** ablakban.

    ![Az Android Studio üdvözlőablakának képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Megjelenik a **projekt kiválasztása** varázsló. A tevékenység kiválasztása mezőben válassza a **telefon és a tábla** és az **üres tevékenység** lehetőséget. Kattintson a **Tovább** gombra.

   ![A projekt kiválasztása varázsló képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. A **projekt konfigurálása** *képernyőn írja be* a gyors útmutató **nevet** , és írja be a *Samples.Speech.cognitiveservices.microsoft.com* **nevet.** Ezután válasszon ki egy projekt könyvtárat. A **minimális API-szint** esetében válassza az **API 23: Android 6,0 (Marshmallow)** elemet. Hagyja üresen az összes többi jelölőnégyzetet, és válassza a **Befejezés** lehetőséget.

   ![A projekt konfigurálása varázsló képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Az Android Studio előkészíti az új Android-projektet. Ezután konfigurálja a projektet, és Ismerje meg az Azure Cognitive Services Speech SDK-t és a Java 8 használatát.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

A Cognitive Services Speech SDK aktuális verziója 1.15.0.

Az Androidhoz készült Speech SDK a szükséges kódtárakat és a szükséges [Android-engedélyeket](https://developer.android.com/studio/projects/android-library)tartalmazza.
A szolgáltatás egy Maven-tárházban üzemel, https: \/ /csspeechstorage.blob.Core.Windows.net/Maven/.

Készítse elő a projektet a Speech SDK használatára. Nyissa meg a **projekt szerkezete** ablakot   >  a Android Studio menüsávon a fájl **projekt szerkezete** lehetőség kiválasztásával. A **projekt szerkezete** ablakban végezze el a következő módosításokat:

1. Az ablak bal oldalán található listából válassza ki a **Project** (Projekt) elemet. Szerkessze az **alapértelmezett függvénytár-tárház** beállításait úgy, hogy hozzáfűz egy vesszőt és a Maven-tárház URL-címét szimpla idézőjelek közé: "https: \/ /csspeechstorage.blob.Core.Windows.net/Maven/"

   ![A Project Structure (Projektstruktúra) ablak képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Ugyanazon a képernyőn a bal oldalon válassza az **alkalmazás** lehetőséget. Ezután kattintson a **Dependencies** (Függőségek) fülre az ablak felső részén. Válassza a zöld pluszjelet ( **+** ), majd a legördülő menüből válassza a **könyvtár függőség** lehetőséget.

   ![A könyvtár függőségének képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. A megjelenő ablakban adja meg az Androidhoz készült Speech SDK nevét és verzióját ( *com. microsoft. cognitiveservices. Speech: Client-SDK: 1.15.0*). Ez után válassza az **OK** gombot.
   A beszédfelismerési SDK-t most a következő módon kell hozzáadni a függőségek listájához:

   ![Képernyőkép a Speech SDK-ról a függőségek listájában](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Válassza a **Tulajdonságok** lapot. A **forrás-kompatibilitás** és a **cél kompatibilitás** érdekében válassza a **1,9** lehetőséget.

   ![Képernyőkép a forrás kompatibilitásáról és a cél kompatibilitásáról](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Kattintson az **OK** gombra a **projekt szerkezete** ablak bezárásához, és alkalmazza a módosításokat a projektre.
