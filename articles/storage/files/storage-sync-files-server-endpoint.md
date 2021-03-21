---
title: Azure File Sync kiszolgáló-végpont hozzáadása/eltávolítása | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá vagy távolíthat el egy kiszolgálói végpontot Azure File Sync használatával. A kiszolgálói végpont a regisztrált kiszolgálók egy adott helye, például a kiszolgáló kötetén található mappa.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f75f0d1ae12db11590f8ce62f3c7b4c0f3e12817
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013938"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Azure File Sync kiszolgálói végpont hozzáadása/eltávolítása
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ez a Windows-kiszolgálók Azure-fájlmegosztás gyors gyorsítótárba alakításával végezhető el. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A *kiszolgálói végpont* a *regisztrált kiszolgáló* egy adott helyét jelöli, például egy kiszolgálón lévő vagy a kötet gyökerét tartalmazó mappát. Ugyanazon a köteten több kiszolgálói végpont is létezhet, ha a névterek nincsenek átfedésben (például F:\sync1 és F:\sync2), és az egyes végpontok egy egyedi szinkronizálási csoportba vannak szinkronizálva. A felhő-előállítók házirendjeit egyenként is konfigurálhatja az egyes kiszolgálói végpontokhoz. Ha olyan kiszolgálói helyet ad hozzá egy meglévő fájlhoz, amely kiszolgálói végpontként egy szinkronizálási csoportba kerül, akkor ezeket a fájlokat a rendszer egyesíti a szinkronizálási csoport többi végpontján már más fájlokkal.

A Azure File Sync végpontok üzembe helyezésével kapcsolatos információkért tekintse meg a [Azure file Sync üzembe helyezését](storage-sync-files-deployment-guide.md) ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek
Kiszolgálói végpont létrehozásához először győződjön meg arról, hogy teljesülnek a következő feltételek: 
- A kiszolgálón telepítve van a Azure File Sync ügynök, és regisztrálva van. A Azure File Sync-ügynök telepítésére vonatkozó utasítások a [kiszolgáló regisztrálása/regisztrációjának megszüntetése Azure file Sync](storage-sync-files-server-registration.md) cikkben találhatók. 
- Győződjön meg arról, hogy a Storage Sync szolgáltatás telepítve van. A Storage Sync szolgáltatás üzembe helyezésével kapcsolatos részletekért tekintse meg a [Azure file Sync központi telepítését](storage-sync-files-deployment-guide.md) ismertető témakört. 
- Győződjön meg arról, hogy a szinkronizálási csoport telepítve van. Megtudhatja, hogyan [hozhat létre szinkronizálási csoportot](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Ellenőrizze, hogy a kiszolgáló csatlakozik-e az internethez, és hogy az Azure elérhető-e. A kiszolgáló és a szolgáltatás közötti kommunikációhoz a 443-es portot használjuk.

## <a name="add-a-server-endpoint"></a>Kiszolgálói végpont felvétele
Kiszolgálói végpont hozzáadásához navigáljon a kívánt szinkronizálási csoportba, és válassza a "kiszolgálói végpont hozzáadása" lehetőséget.

![Új kiszolgálói végpontok hozzáadása a szinkronizálási csoport panelen](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

A **kiszolgáló-végpont hozzáadása** területen a következő információk szükségesek:

- **Regisztrált kiszolgáló**: annak a kiszolgálónak vagy fürtnek a neve, amelyen létre kívánja hozni a kiszolgálói végpontot.
- **Elérési út**: a szinkronizálási csoport részeként szinkronizálandó Windows Server elérési útja.
- **Felhőbeli rétegek**: a felhőalapú rétegek engedélyezésére vagy letiltására szolgáló kapcsoló. Ha ez a beállítás engedélyezve van, a Felhőbeli rétegek a fájlokat az Azure-fájlmegosztás *szintjére* fogják felvenni. Ez átalakítja a helyszíni fájlmegosztást egy gyorsítótárba, és nem az adatkészlet teljes másolatát, így segít a hely hatékonyságának kezelésében a kiszolgálón.
- **Kötet szabad területe**: a kiszolgálói végpontot tároló köteten foglalható szabad terület mennyiségét. Ha például a kötet szabad területe 50%-ra van állítva egy olyan köteten, amely egyetlen kiszolgálói végponttal rendelkezik, az adatmennyiség nagyjából fele lesz a Azure Files. Függetlenül attól, hogy engedélyezve van-e a felhőalapú rétegek használata, az Azure-fájlmegosztás mindig a szinkronizálási csoportban lévő összes adattal rendelkezik.

Válassza a **Létrehozás** lehetőséget a kiszolgálói végpont hozzáadásához. A szinkronizálási csoport névterében lévő fájlok már szinkronban lesznek tárolva. 

## <a name="remove-a-server-endpoint"></a>Kiszolgálói végpont eltávolítása
Ha azt szeretné, hogy a Azure File Sync egy adott kiszolgálói végponton ne használja, akkor eltávolíthatja a kiszolgálói végpontot. 

> [!Warning]  
> Ne kísérelje meg a szinkronizálás, a felhő-rétegek vagy a Azure File Sync bármely más aspektusa hibáinak elhárítását azáltal, hogy eltávolítja és újból létrehozza a kiszolgálói végpontot, hacsak a Microsoft mérnöke kifejezetten nem utasította. A kiszolgálói végpontok eltávolítása egy romboló művelet, és a kiszolgálói végponton belül a lépcsőzetesen megadott fájlok nem lesznek "újracsatlakoztatva" az Azure-fájlmegosztás helyeihez a kiszolgálói végpont újbóli létrehozása után, ami szinkronizálási hibákat eredményez. Azt is vegye figyelembe, hogy a kiszolgálói végpont névterén kívül található, többrészes fájlok is véglegesen elvesznek. A többszintes fájlok a kiszolgálói végponton belül létezhetnek, még akkor is, ha a felhő-rétegek nem voltak engedélyezve.

Annak biztosítása érdekében, hogy a rendszer az összes rétegű fájlt visszahívja a kiszolgálói végpont eltávolítása előtt, tiltsa le a felhő-előírásokat a kiszolgálói végponton, majd hajtsa végre a következő PowerShell-parancsmagot a kiszolgálói végpont névterében lévő összes rétegű fájl visszahívásához:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
A megadásával `-Order CloudTieringPolicy` a rendszer visszahívja a legutóbb módosított fájlokat.
További választható, de hasznos paraméterek:
* `-ThreadCount` meghatározza, hogy hány fájlt lehet visszahívni párhuzamosan.
* `-PerFileRetryCount`meghatározza, hogy a rendszer milyen gyakran próbálkozzon a visszahívással egy jelenleg blokkolt fájlon.
* `-PerFileRetryDelaySeconds`meghatározza azt az időtartamot másodpercben, ameddig a rendszer újrahívja az újrapróbálkozási kísérleteket, és mindig az előző paraméterrel együtt kell használni őket.

> [!Note]  
> Ha a kiszolgálót üzemeltető helyi köteten nincs elég szabad hely az összes rétegű adat felidézéséhez, a `Invoke-StorageSyncFileRecall` parancsmag meghiúsul.  

A kiszolgálói végpont eltávolítása:

1. Navigáljon a Storage Sync szolgáltatáshoz, ahol a kiszolgáló regisztrálva van.
2. Navigáljon a kívánt szinkronizálási csoporthoz.
3. A Storage Sync szolgáltatás szinkronizálás csoportjában távolítsa el a kívánt kiszolgálói végpontot. Ezt úgy érheti el, ha a jobb gombbal a megfelelő kiszolgálói végpontra kattint a szinkronizálási csoport ablaktáblán.

    ![Kiszolgálói végpont eltávolítása egy szinkronizálási csoportból](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Következő lépések
- [Kiszolgáló regisztrálása/regisztrációjának törlése Azure File Sync](storage-sync-files-server-registration.md)
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
