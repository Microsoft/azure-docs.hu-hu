---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "77123156"
---
Az Azure File Sync-ügynök rendszeres időközönként frissül új funkciók hozzáadásával és a problémák megoldásával. Javasoljuk, hogy konfigurálja Microsoft Update a Azure File Sync-ügynök frissítéseinek elérhetővé tételéhez.

#### <a name="major-vs-minor-agent-versions"></a>Fő vagy másodlagos ügynök verziói
* A fő ügynök verziói gyakran tartalmaznak új funkciókat, és a verziószám első részeként növekvő számmal rendelkeznek. Például: \* 2. \* .\*\*
* A másodlagos ügynök verzióit "javítások" is nevezik, és a főverziónál gyakrabban jelennek meg. Gyakran tartalmaznak hibajavításokat és kisebb javításokat, de nincsenek új funkciók. Például: \* \* . 3.\*\*

#### <a name="upgrade-paths"></a>Frissítési útvonalak
A Azure File Sync ügynök frissítéseinek telepítése négy jóváhagyott és tesztelt módon történik. 
1. **Előnyben részesített Microsoft Update konfigurálása az ügynök frissítéseinek automatikus letöltésére és telepítésére.**  
    Mindig javasoljuk, hogy minden Azure File Sync frissítéssel ellenőrizze, hogy van-e hozzáférése a Server Agent legújabb frissítéseihez. Microsoft Update a folyamat zökkenőmentesen, automatikusan letölti és telepíti a frissítéseket.
2. **A AfsUpdater.exe használatával letöltheti és telepítheti az ügynökök frissítéseit.**  
    A AfsUpdater.exe az ügynök telepítési könyvtárában található. Az ügynök frissítéseinek letöltéséhez és telepítéséhez kattintson duplán a végrehajtható fájlra. 
3. **Meglévő Azure File Sync ügynök javítása egy Microsoft Update-javítócsomag vagy egy. msp végrehajtható fájl használatával. A legújabb Azure File Sync frissítési csomag letölthető a [Microsoft Update katalógusból](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Az. msp végrehajtható fájl futtatása a Azure File Sync telepítését az előző frissítési útvonalon Microsoft Update által automatikusan használt módszerrel fogja frissíteni. A Microsoft Update-javítások alkalmazása a Azure File Sync telepítés helyben történő frissítését végzi.
4. **Töltse le a legújabb Azure File Sync Agent telepítőjét a [Microsoft letöltőközpontból](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Meglévő Azure File Sync ügynök telepítésének frissítéséhez távolítsa el a régebbi verziót, majd telepítse a legújabb verziót a letöltött telepítőből. A kiszolgáló regisztrációját, szinkronizálási csoportjait és egyéb beállításait a Azure File Sync-telepítő tartja karban.

#### <a name="automatic-agent-lifecycle-management"></a>Automatikus ügynök-életciklus kezelése
Az ügynök 6-os verziójában a file Sync csapata bevezette az ügynök automatikus frissítési funkcióját. Két mód közül választhat, és megadhat egy karbantartási időszakot, amelyben a frissítés a kiszolgálón próbálkozik. Ez a szolgáltatás úgy lett kialakítva, hogy segítse az ügynök életciklus-felügyeletét azáltal, hogy a Guardrail megakadályozza az ügynök lejáratát, vagy egy nem problémamentes, maradjon a jelenlegi beállítás.
1. Az **alapértelmezett beállítás** azt kísérli meg, hogy megakadályozza az ügynök lejáratát. Az ügynök az elküldött lejárati dátumtól számított 21 napon belül az ügynök megkísérli a saját frissítését. A szolgáltatás a lejárat előtt, illetve a kiválasztott karbantartási időszakon belül egy héttel a frissítés előtt elindít egy, a lejáratot követő 21 napon belüli frissítést. **Ez a beállítás nem teszi feleslegessé a rendszeres Microsoft Update javítások meghozatalának szükségességét.**
1. Megadhatja azt is, hogy az ügynök automatikusan frissítse magát, amint egy új ügynök verziója elérhetővé válik (jelenleg nem alkalmazható fürtözött kiszolgálókra). Ez a frissítés a kiválasztott karbantartási időszak alatt jelenik meg, és lehetővé teszi a kiszolgáló számára, hogy az általánosan elérhetővé válás után kihasználhassa az új szolgáltatásokat és fejlesztési funkciókat. Ez az ajánlott, gond nélküli beállítás, amely a fő ügynök-verziókat, valamint a kiszolgáló rendszeres frissítési javításait biztosítja. Minden ügynök megjelent a GA minőségben. Ha ezt a lehetőséget választja, a Microsoft az ügynök legújabb verzióját fogja elrepülni Önnek. A fürtözött kiszolgálók ki vannak zárva. A Berepülés befejezése után az ügynök elérhetővé válik a [Microsoft letöltőközpontban](https://go.microsoft.com/fwlink/?linkid=858257) aka.MS/AFS/Agent is.

 ##### <a name="changing-the-auto-upgrade-setting"></a>Az automatikus frissítési beállítás módosítása

Az alábbi utasítások azt írják le, hogyan módosíthatja a beállításokat a telepítő befejezése után, ha módosítania kell.

Nyisson meg egy PowerShell-konzolt, és keresse meg azt a könyvtárat, ahová a Szinkronizáló ügynököt telepítette, majd importálja a kiszolgálói parancsmagokat. Alapértelmezés szerint ez a következőképpen néz ki:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

A futtatásával `Get-StorageSyncAgentAutoUpdatePolicy` ellenőrizze az aktuális házirend-beállítást, és határozza meg, hogy szeretné-e módosítani.

Ha módosítani szeretné a jelenlegi házirend-beállítást a késleltetett frissítés nyomon követésére, a következőt használhatja:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Ha módosítani szeretné az aktuális házirend-beállítást az azonnali frissítés nyomon követésére, a következőt használhatja:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Az ügynök életciklusa és a változás-kezelési garanciái
A Azure File Sync egy felhőalapú szolgáltatás, amely folyamatosan bemutatja az új szolgáltatásokat és a továbbfejlesztett funkciókat. Ez azt jelenti, hogy egy adott Azure File Sync ügynök verziója csak korlátozott ideig használható. Az üzembe helyezés megkönnyítéséhez a következő szabályok garantálják, hogy elegendő idő és értesítés szükséges ahhoz, hogy az ügynökök frissítései/frissítései a változás-kezelési folyamat során is megfeleljenek:

- A fő ügynök verziói a kezdeti kiadás dátumától számítva legalább hat hónapig támogatottak.
- Garantáljuk, hogy a főbb ügynökök verzióinak támogatása között legalább három hónapig átfedésben van. 
- A figyelmeztetéseket a rendszer a hamarosan lejárt ügynökkel rendelkező regisztrált kiszolgálók számára adja ki, legalább három hónappal a lejárat előtt. Megtekintheti, hogy egy regisztrált kiszolgáló az ügynök egy régebbi verzióját használja-e a Storage Sync szolgáltatás regisztrált kiszolgálók szakasza alatt.
- A másodlagos ügynök verziójának élettartama a társított főverzióhoz van kötve. Ha például az ügynök 3,0-es verziója megjelent, az ügynök 2. verzió. \* az összes beállítása együtt jár le.

> [!Note]
> A lejárati figyelmeztetést tartalmazó ügynök verziójának telepítése figyelmeztetést jelenít meg, de sikeres lesz. A lejárt ügynök verziójának telepítésére vagy kapcsolódására tett kísérlet nem támogatott, és le lesz tiltva.
