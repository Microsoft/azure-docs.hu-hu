---
title: Horgony keresése stratégia
description: Ismerje meg a keresés API meghívásakor a különböző stratégiákat
author: pamistel
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: pamistel
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 13aa12be5a336363bbe3bcbf3e3fb354a8fa3074
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048476"
---
# <a name="understanding-the-anchorlocatecriteria-class"></a>A AnchorLocateCriteria osztály ismertetése
Ebben a cikkben megismerheti a horgonyok lekérdezéséhez használható különböző lehetőségeket. A AnchorLocateCriteria osztályt, annak lehetőségeit és érvényes kombinációit fogjuk megtenni.

## <a name="anchor-locate-criteria"></a>Horgony megkeresésének feltételei
A [AnchorLocateCriteria osztály](/dotnet/api/microsoft.azure.spatialanchors.anchorlocatecriteria) segítségével lekérdezheti a korábban létrehozott horgonyok szolgáltatását. Egy AnchorLocateCriteria-objektum bármikor felhasználható egy figyelőre. Minden AnchorLocateCriteria objektumnak pontosan a következő tulajdonságok **egyikét** kell tartalmaznia: [azonosítók](#identifiers), [NearAnchor](#nearanchor)vagy [NearDevice](#neardevice). Szükség esetén további tulajdonságok is megadhatók, például a [stratégia](#strategy), a [BypassCache](#bypasscache)és a [RequestedCategories](#requestedcategories) . 

### <a name="properties"></a>Tulajdonságok
Adja meg pontosan az alábbi tulajdonságok **egyikét** a figyelőben:
#### <a name="identifiers"></a>Azonosítók
*Alapértelmezett érték: üres karakterlánc-tömb*

Az azonosítók használatával megadhatja a megkeresni kívánt horgonyokhoz tartozó horgony-azonosítók listáját. A horgony-azonosítók kezdetben a sikeres horgony létrehozása után lesznek visszaadva. A megadott azonosítókkal a AnchorLocateCriteria korlátozza a kért horgonyok készletét a megfelelő szerkesztőpont-azonosítókkal rendelkező horgonyokra. Ez a tulajdonság karakterlánc-tömb használatával van megadva. 

#### <a name="nearanchor"></a>NearAnchor
*Alapértelmezett érték: nincs beállítva*

A NearAnchor segítségével megadhatja, hogy a AnchorLocateCriteria korlátozza a kért horgonyok készletét a kiválasztott horgonytól távol lévő kívánt távolságra. Ezt a kiválasztott horgonyt a forrás-horgonyként kell megadnia. A keresés további korlátozásához a forrás-és a visszaadott horgonyok maximális számát is beállíthatja.
Ez a tulajdonság egy NearAnchorCriteria objektummal van megadva.

#### <a name="neardevice"></a>NearDevice
*Alapértelmezett érték: nincs beállítva*

A NearDevice segítségével megadhatja, hogy a AnchorLocateCriteria korlátozza a kért horgonyok készletét az eszköz fizikai helyére közel lévő értékekre. Az eszközön található horgonyok felderítéséhez az összes engedélyezett érzékelőt fogja használni a rendszer. Ahhoz, hogy a legjobb eséllyel megtalálják a horgonyokat, konfigurálnia kell a SensorCapabilities, hogy a munkamenet hozzáférjen az összes megfelelő érzékelőkhöz. A tulajdonság beállításával és használatával kapcsolatos további információkért lásd: [durva újrahonosítás – Azure térbeli horgonyok | Microsoft docs](./coarse-reloc.md) , és hogyan hozhatók létre és találhatók az olyan horgonyok, amelyek [a C#](../how-tos/set-up-coarse-reloc-unity.md), az [Objective-C](../how-tos/set-up-coarse-reloc-unity.md), a [Swift](../how-tos/set-up-coarse-reloc-swift.md), a [Java](../how-tos/set-up-coarse-reloc-java.md), a [c++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)és a [c++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md) *használatával történő durva újrahonosítást használnak* .
Ez a tulajdonság egy NearDeviceCriteria objektummal van megadva.

### <a name="additional-properties"></a>További tulajdonságok
#### <a name="bypasscache"></a>BypassCache
*Alapértelmezett érték: false*

Amikor létrehoztak egy horgonyt vagy megtaláltak egy munkamenetben, azt a gyorsítótárban is tárolja a rendszer.  Ha ez a tulajdonság hamis értékre van állítva, akkor az ugyanabban a munkamenetben lévő minden további lekérdezés visszaadja a gyorsítótárazott értéket. Nem történt kérelem az ASA szolgáltatáshoz.

#### <a name="requestedcategories"></a>RequestedCategories
*Alapértelmezett érték: Properties | Térbeli*

Ezzel a tulajdonsággal határozható meg, hogy milyen adatok legyenek visszaadva egy lekérdezésből a AnchorLocateCriteria használatával. Az alapértelmezett érték mindkét tulajdonságot és térbeli értéket adja vissza, ez nem módosítható, ha a tulajdonságok és a térbeli adatok egyaránt szükségesek. Ez a tulajdonság a AnchorDataCategory Enum használatával adható meg.

AnchorDataCategory enumerálási értéke | Visszaadott adatok
-----|------------
Nincsenek | Nincs visszaadott érték
Tulajdonságok| A horgony tulajdonságai, beleértve a AppProperties is, visszaadva.
Térbeli| A rendszer a horgonyra vonatkozó térbeli adatokat adja vissza.

#### <a name="strategy"></a>Stratégia
*Alapértelmezett érték: AnyStrategy*

A stratégia részletesebben határozza meg a horgonyok elhelyezkedését. A stratégia tulajdonságot LocateStrategy Enum használatával lehet megadni.

LocateStrategy enumerálási értéke | Leírás
---------------|------------
AnyStrategy | Ez a stratégia lehetővé teszi, hogy a rendszerek a VisualInformation és a kapcsolati stratégiák kombinációit használják a horgonyok kereséséhez. 
VisualInformation|Ez a stratégia megkísérli megtalálni a horgonyokat az aktuális környezetből származó vizualizációs információk alapján a horgony vizualizációs lábnyomának megfelelő értékekkel. A horgony vizualizációs lábnyoma a horgonyhoz jelenleg kapcsolódó vizualizációs információkra utal. Ez a vizualizációs információ általában azonban nem gyűjti össze kizárólag a horgony létrehozásakor. Ez a stratégia jelenleg csak a NearDevice vagy az azonosítók tulajdonságaival együtt engedélyezett.
Kapcsolat|Ez a stratégia a meglévő [csatlakoztatott horgonyok](./anchor-relationships-way-finding.md#connect-anchors)használatával megkísérli megtalálni a horgonyokat. Ez a stratégia jelenleg csak a NearAnchor vagy az azonosítók tulajdonságaival együtt engedélyezett. Az azonosítók tulajdonság használata esetén szükség van arra, hogy ugyanabban a munkamenetben a felhasználónak előzőleg olyan horgony (ka) t kell megadnia, amelyen már létrejöttek az azonosítók tömbben megadott azonosítók. 


### <a name="valid-combinations-of-locatestrategy-and-anchorlocatecriteria-properties"></a>A LocateStrategy és a AnchorLocateCriteria tulajdonságainak érvényes kombinációi 

A rendszer jelenleg nem teszi lehetővé a stratégia és a AnchorLocateCriteria tulajdonságainak kombinációját. A következő táblázat a megengedett kombinációkat tartalmazza:



Tulajdonság | AnyStrategy | Kapcsolat | VisualInformation
-------- | ------------|--------------|-------------------
Azonosítók | &check;    | &check;     | &check;
NearAnchor  | &check;   (az alapértelmezett érték a kapcsolat) | &check;    | 
NearDevice  | &check;    |   | &check;




## <a name="next-steps"></a>Következő lépések

A AnchorLocateCriteria osztály használatával további példákat talál az [Azure térbeli horgonyokat használó horgonyok létrehozásához és megkereséséhez](../create-locate-anchors-overview.md) .