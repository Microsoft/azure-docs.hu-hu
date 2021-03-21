---
title: Kibocsátási megjegyzések a Microsoft. ApplicationInsights. Snapshotcollector nugetcsomag NuGet-csomaghoz – Application Insights
description: A Application Insights Snapshot Debugger által használt Microsoft. ApplicationInsights. Snapshotcollector nugetcsomag NuGet-csomag kibocsátási megjegyzései.
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: 4a787c6e2a9b59874f965a2bbcebea9ce02d8082
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97093260"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>A Microsoft. ApplicationInsights. Snapshotcollector nugetcsomag kibocsátási megjegyzései

Ez a cikk a .NET-alkalmazások Microsoft. ApplicationInsights. Snapshotcollector nugetcsomag NuGet csomagjának kiadási megjegyzéseit tartalmazza, amelyeket a Application Insights Snapshot Debugger használ.

[További információ](./snapshot-debugger.md) a .NET-alkalmazások Application Insights Snapshot Debuggeréről.

Hibajelentések és visszajelzések esetén nyisson meg egy problémát a GitHubon https://github.com/microsoft/ApplicationInsights-SnapshotCollector

## <a name="release-notes"></a>Kibocsátási megjegyzések

## <a name="1375"></a>[1.3.7.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.5)
Egy pont kiadása, hogy vezetnie a 1.4.0-pre javítását.
### <a name="bug-fixes"></a>Hibajavítások
- Javítsa ki [a ObjectDisposedException a Leállítás](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2097)során.

## <a name="1374"></a>[1.3.7.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.4)
Egy pont kiadása, amely a Azure App Service kódolású csatolási forgatókönyvének tesztelése során felderített problémák megoldására mutat.
### <a name="changes"></a>Módosítások
- A netcoreapp 3.0 célja most a Microsoft. ApplicationInsights. AspNetCore >= 2.1.1 (korábban >= 2.1.2) függvénytől függ.

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
Egy pont kiadása, amely néhány nagy hatású problémát érint.
### <a name="bug-fixes"></a>Hibajavítások
- Rögzített PDB-felderítés a WWWroot/bin mappában, amely megszakadt, amikor a 1.3.6-ben megváltoztatta a Symbol keresési algoritmust.
- Rögzített zajos ExtractWasCalledMultipleTimesException a telemetria-ben.

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>Módosítások
- A Snapshotcollector nugetcsomag netcoreapp 2.0-s célja a Microsoft. ApplicationInsights. AspNetCore >= 2.1.1 (Again) függvénytől függ. Ez a viselkedést a 1.3.5 előtti állapotra vált. Megpróbáljuk frissíteni a 1.3.6-ben, de kitört néhány Azure App Service forgatókönyvet.
### <a name="new-features"></a>Új funkciók
- Snapshot Collector beolvassa és elemzi a ConnectionString-t a APPLICATIONINSIGHTS_CONNECTION_STRING környezeti változóból vagy a TelemetryConfiguration. Ez elsősorban arra szolgál, hogy a végpontot állítsa be a pillanatkép-szolgáltatáshoz való csatlakozáshoz. További információ a [kapcsolatok karakterláncait ismertető dokumentációban](./sdk-connection-string.md)található.
### <a name="bug-fixes"></a>Hibajavítások
- Átváltva a HttpClient használatára az összes cél esetében, kivéve a Net45, mert egy nem kompatibilis SecurityProtocol miatt sikertelen volt a webkérelmek végrehajtása (TLS 1,2 szükséges).

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>Módosítások
- A Snapshotcollector nugetcsomag mostantól függ a Microsoft. ApplicationInsights >= 2.5.1-től az összes cél keretrendszerhez. Ez akkor fordulhat elő, ha az alkalmazás a Microsoft. ApplicationInsights SDK egy régebbi verziójától függ.
- Távolítsa el a TLS 1,0 és a 1,1 támogatását a pillanatkép-Feltöltőben.
- A PDB-vizsgálatok időszaka mostantól 15 perc helyett 24 órát vesz igénybe. Konfigurálható a PdbRescanInterval on SnapshotCollectorConfiguration használatával.
- A PDB-vizsgálat csak a legfelső szintű mappákat keresi meg rekurzív helyett. Ez akkor fordulhat elő, ha a szimbólumok a bináris mappa almappáiban találhatók.
### <a name="new-features"></a>Új funkciók
- A naplófájlok elforgatása a SnapshotUploader-ben a régi fájlokkal rendelkező naplók mappa kitöltésének elkerülése érdekében.
- A .NET Core 3,0-alkalmazásokhoz való deoptimalizálási támogatás (a Attach ReJIT használatával).
- Szimbólumok hozzáadása a NuGet-csomaghoz.
- További metaadatok megadása a minidumps feltöltésekor.
- Hozzáadott egy inicializált tulajdonságot a SnapshotCollectorTelemetryProcessor. Ez egy CancellationToken, amely a Snapshot Collector teljes inicializálásakor és a szolgáltatási végponthoz való csatlakozáskor megszakad.
- Mostantól a dinamikusan generált metódusokban lévő kivételekhez is készíthetők Pillanatképek. Például Entity Framework lekérdezések által generált lefordított kifejezéseket tartalmazó fák.
### <a name="bug-fixes"></a>Hibajavítások
- A AmbiguousMatchException betöltése Állapotmonitor miatt Snapshot Collector.
- A GetSnapshotCollector-bővítmény metódus most megkeresi az összes TelemetrySinks.
- Ne indítsa el a pillanatkép-feltöltőt nem támogatott platformokon.
- Kezelje a InvalidOperationException a dinamikus metódusok (például Entity Framework) deoptimalizálásakor

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- A szuverén felhők támogatásának hozzáadása (a régebbi verziók nem működnek a szuverén felhőkben)
- A AddSnapshotCollector () használatával megkönnyíti a pillanatkép-gyűjtő hozzáadását. További információt [itt](./snapshot-debugger-appservice.md) találhat.
- A FISMA MD5-beállítás használatával ellenőrizheti a blob-blokkokat. Ezzel elkerülhető az alapértelmezett .NET MD5 titkosítási algoritmus, amely nem érhető el, ha az operációs rendszer FIPS-kompatibilis módra van beállítva.
- A .NET-keretrendszerbeli keretek figyelmen kívül hagyása a függvények meghívásakor. Ezt a viselkedést a DeoptimizeIgnoredModules konfigurációs beállítása szabályozhatja.
- Adjon hozzá `DeoptimizeMethodCount` olyan konfigurációs beállítást, amely több függvényhívás deoptimalizálását teszi lehetővé. További információ itt

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- Strukturált rendszerállapot-kulcsok engedélyezése.
- Növelje a SnapshotUploader megbízhatóságát – akkor is folytassa az indítást, ha a régi feltöltő naplókat nem lehet áthelyezni.
- További telemetria újbóli engedélyezése, ha a SnapshotUploader.exe azonnal kilép (az 1.3.3-ben le lett tiltva).
- Egyszerűsítse a belső telemetria.
- _Kísérleti funkció_: Snappoint-gyűjtési csomagok: add "snapshotOnFirstOccurence". További információk [itt](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)érhetők el.

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- Kijavítva a hiba, amely miatt a SnapshotUploader.exe nem válaszol, és nem tölthet fel pillanatképeket a .NET Core-alkalmazásokhoz.

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _Kísérleti funkció_: Snappoint-gyűjtési csomagok. További információk [itt](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)érhetők el.
- A SnapshotUploader.exe kilép, amikor a futtatókörnyezet kitölti a alkalmazástartomány, amelyből a Snapshotcollector nugetcsomag betöltődik, ahelyett, hogy a folyamat kilépését kellene várnia. Ez javítja a gyűjtő megbízhatóságát az IIS-ben üzemeltetett környezetben.
- A konfiguráció hozzáadásával engedélyezheti, hogy több Snapshotcollector nugetcsomag-példány ugyanazt a kialakítási kulcsot használja a SnapshotUploader-folyamat megosztásához: ShareUploaderProcess (alapértelmezett érték `true` ).
- További telemetria jelent, amikor a SnapshotUploader.exe azonnal kilép.
- Csökkenthető a támogatási fájlok száma, SnapshotUploader.exe a lemezre kell írni.

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- Távolítsa el a pillanatképek gyűjtésének támogatását a RtlCloneUserProcess API-val, és csak a PssCaptureSnapshots API-t támogatja.
- Növelje meg az alapértelmezett korlátot, hogy hány pillanatképet lehet rögzíteni 10 percen belül 1 és 3 között.
- A TLS 1,1 és 1,2 közötti egyeztetés engedélyezése SnapshotUploader.exe
- További telemetria jelentenek, amikor a SnapshotUploader figyelmeztetést vagy hibát naplóz
- A pillanatképek leállítása, amikor a háttérrendszer jelentést készít a napi kvóta elérésekor (naponta 50 pillanatkép)
- Ha nem szeretné, hogy a két példány egyszerre fusson, vegyen fel további beadást SnapshotUploader.exe.

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>Módosítások
- A .NET-keretrendszert célzó alkalmazások esetében a Snapshot Collector mostantól a Microsoft. ApplicationInsights 2.3.0 vagy újabb verziójától függ.
A rendszer 2.2.0 vagy újabb verzióra használta.
Úgy véljük, ez a legtöbb alkalmazás esetében nem jelent problémát, de tudassa velünk, ha ez a változás megakadályozza a legújabb Snapshot Collector használatát.
- A sikertelen feltöltések újrapróbálkozásakor használjon exponenciális visszatartási késéseket a pillanatkép-Feltöltőben.
- A telemetria megbízhatóbb jelentéskészítéséhez használja a ServerTelemetryChannel (ha elérhető).
- Használja a "SdkInternalOperationsMonitor" lehetőséget a Snapshot Debugger szolgáltatáshoz való kezdeti kapcsolaton, hogy a függőségi követés figyelmen kívül hagyja.
- A Snapshot Debugger szolgáltatás kezdeti kapcsolata körüli telemetria javítása.
- További telemetria jelentése a következőhöz:
  - Azure App Service verziója.
  - Azure számítási példányok.
  - Konténerek.
  - Azure Function-alkalmazás.
### <a name="bug-fixes"></a>Hibajavítások
- Ha a probléma számlálójának alaphelyzetbe állítási időköze 24 nap, az értelmezést 24 órára állítja.
- Kijavítva egy olyan hibát, amelyben a pillanatkép-feltöltő leállítja az új Pillanatképek feldolgozását, ha kivétel történt egy pillanatkép megsemmisítése közben.

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- A pillanatkép-feltöltő bináris fájljaival javítsa az erős nevek aláírását.

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>Módosítások
- A SnapshotUploader (64). exe fájlokhoz szükséges fájlok mostantól erőforrásként vannak beágyazva a fő DLL-ben. Ez azt jelenti, hogy a SnapshotCollectorFiles mappa már nem jön létre, leegyszerűsíti a kiépítést és a telepítést, és csökkenti a Megoldáskezelő zsúfoltságát. Ügyeljen a fájl változásainak áttekintésére a frissítésekor `.csproj` . A `Microsoft.ApplicationInsights.SnapshotCollector.targets` fájlra már nincs szükség.
- A telemetria akkor is naplózva van a Application Insights erőforrásban, ha a ProvideAnonymousTelemetry értéke false (hamis). Így is megvalósítható az állapot-ellenőrzési funkció a Azure Portal. A ProvideAnonymousTelemetry csak a Microsoftnak eljuttatott telemetria érinti a terméktámogatás és a fejlesztés érdekében.
- Ha a TempFolder vagy a ShadowCopyFolder átirányítják a környezeti változókba, tartsa tétlenül a gyűjtőt addig, amíg a környezeti változók be nem állnak.
- Az internethez proxykiszolgálón keresztül csatlakozó alkalmazások esetén Snapshot Collector automatikusan felismeri a proxybeállításokat, és átadja őket a SnapshotUploader.exenak.
- Csökkentse a SnapshotUplaoder folyamat prioritását (ahol lehetséges). Ez a prioritás felülbírálható a IsLowPrioirtySnapshotUploader kapcsoló használatával.
- GetSnapshotCollector-bővítményi metódust adott hozzá a TelemetryConfiguration olyan forgatókönyvekhez, amelyeken programozott módon szeretné konfigurálni a Snapshot Collector.
- Állítsa be a Application Insights SDK verzióját (az alkalmazás verziója helyett) az ügyfél felé irányuló telemetria.
- Küldje el az első szívverési eseményt két perc múlva.
### <a name="bug-fixes"></a>Hibajavítások
- Javítsa ki a NullReferenceException, ha a kivételek null vagy nem módosítható adatszótárakkal rendelkeznek.
- A feltöltőben próbálja megismételni a PDB-t, ha megosztási szabálysértést kapunk.
- A duplikált telemetria kijavítása, amikor az indításkor egynél több szál hív a telemetria folyamatba.

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>Módosítások
- Az XML doc-megjegyzési fájlok mostantól a NuGet csomagban találhatók.
- ExcludeFromSnapshotting-bővítményi metódust adott hozzá olyan `System.Exception` helyzetekben, amikor tudja, hogy van egy zajos kivétel, és el szeretné kerülni a pillanatképek létrehozását.
- Egy IsEnabledWhenProfiling-konfigurációs tulajdonság hozzáadva, az alapértelmezett érték igaz. Ez az előző verziók változása, ahol a pillanatkép-létrehozás átmenetileg le lett tiltva, ha a Application Insights Profiler részletes gyűjteményt hajtott végre. A régi viselkedés helyreállítható úgy, hogy ezt a tulajdonságot hamis értékre állítja.
### <a name="bug-fixes"></a>Hibajavítások
- A SnapshotUploader64.exe megfelelően aláírja.
- Védelmet nyújt a telemetria-feldolgozó kettős inicializálásával szemben.
- A telemetria kettős naplózásának megakadályozása több folyamattal rendelkező alkalmazásokban.
- Javítsa ki a begyűjtési terv lejárati idejét, ami 24 óra elteltével megakadályozhatja a pillanatképek megtartását.

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
Ennek a verziónak a legnagyobb változása (ezért az új alverziószámra való áttérés) a pillanatkép-létrehozási és-kezelési folyamat újraírása. A korábbi verziókban ez a funkció a natív kódban (ProductionBreakpoints *. dll és SnapshotHolder*. exe) lett implementálva. Az új implementáció minden olyan felügyelt kód, amelyben a P/meghívja a t. Ebben az első verzióban az új folyamat használatával az eredeti viselkedéstől távol nem tévedett. Az új implementáció lehetővé teszi a hibajelentések jobb jelentését, és a jövőbeli fejlesztésekhez.

### <a name="other-changes-in-this-version"></a>Egyéb változások ebben a verzióban
- MinidumpUploader.exe átnevezve SnapshotUploader.exere (vagy SnapshotUploader64.exe).
- A kérések deoptimalizálására/újraoptimalizálására szolgáló időzítési telemetria hozzáadva.
- A minidump-feltöltések gzip-tömörítéssel bővült.
- Kijavított egy problémát, amelyben a PDBs zárolták a hely verziófrissítésének megakadályozása érdekében.
- Naplózza az eredeti mappanevet (SnapshotCollectorFiles) az árnyék másolásakor.
- Állítsa be a memória korlátait a 64 bites folyamatokhoz, hogy megakadályozza a hely újraindítását a bácsi miatt.
- Javítsa ki azt a problémát, amelyben a pillanatképek gyűjtése még a letiltás után is megtörtént.
- A szívverési események naplózása az ügyfél AI-erőforrása számára.
- Javítsa a pillanatkép sebességét a "forrás" a probléma-AZONOSÍTÓból való eltávolításával.

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>Módosítások
Kibővített használati telemetria
- A .NET-verzió és az operációs rendszer észlelése és jelentése
- További Azure-környezetek észlelése és jelentése (Cloud Service, Service Fabric)
- Rekordok és jelentések kivételének mérőszámai (az 1. számú TrackException-hívások száma és a meghívások száma) a Szívveréses telemetria.
### <a name="bug-fixes"></a>Hibajavítások
- A SqlException megfelelő kezelését, ahol a belső kivétel (Win32Exception) nincs elvetve.
- A záró szóközök levágása a szimbólum mappáiban, ami a parancssori argumentumok helytelen elemzését eredményezte a MinidumpUploader.
- A Snapshot Debugger ügynök végpontjának sikertelen kapcsolatainak végtelen újrapróbálkozásának megakadályozása.

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>Módosítások
- A gazdagépek memóriájának védelme hozzáadva. Ez a funkció csökkenti a gazdagép memóriájának hatását.
- Javítsa a Azure Portal pillanatkép-megtekintési élményt.