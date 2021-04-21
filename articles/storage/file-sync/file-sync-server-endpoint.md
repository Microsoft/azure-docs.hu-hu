---
title: Új kiszolgálói Azure File Sync hozzáadása/eltávolítása | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá vagy távolíthat el kiszolgálóvégpontokat Azure File Sync. A kiszolgálóvégpont egy adott hely a regisztrált kiszolgálón, például egy mappa egy kiszolgálóköteten.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bcb346782b8d158dab5371a583c5a2576f0a09c9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796813"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Kiszolgálóvégpont hozzáadása Azure File Sync eltávolítása
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ezt úgy teszi meg, hogy a Windows-kiszolgálókat az Azure-fájlmegosztás gyors gyorsítótárába alakítja. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A *kiszolgálóvégpont* egy adott helyet képvisel *a* regisztrált kiszolgálón, például egy mappát egy kiszolgálóköteten, vagy a kötet gyökerét. Több kiszolgálóvégpont is létezhet ugyanazon a köteten, ha a névterek nincsenek átfedésben (például F:\sync1 és F:\sync2), és minden végpont egy egyedi szinkronizálási csoporttal szinkronizál. A felhőbeli rétegezés szabályzatát külön-külön konfigurálhatja az egyes kiszolgálóvégponthoz. Ha olyan kiszolgálóhelyet ad hozzá egy szinkronizálási csoporthoz, amely kiszolgálóvégpontként meglévő fájlkészletet tartalmaz, akkor ezek a fájlok egyesülnek a szinkronizálási csoport más végpontjaiban már meglévő fájlokkal.

A [végpontok Azure File Sync](file-sync-deployment-guide.md) üzembe helyezési Azure File Sync információkért lásd: Azure File Sync üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek
Kiszolgálóvégpont létrehozásához először meg kell győződnie arról, hogy teljesülnek a következő feltételek: 
- A kiszolgálón telepítve van Azure File Sync ügynök, és regisztrálva van. A Azure File Sync ügynök telepítésével kapcsolatos útmutatást a Kiszolgáló regisztrálása vagy regisztrációjának Azure File Sync [talál.](file-sync-server-registration.md) 
- Győződjön meg arról, hogy a Storage Sync Service telepítve van. A Társzinkronizálási szolgáltatás [üzembe helyezéséről Azure File Sync](file-sync-deployment-guide.md) üzembe helyezési információkért lásd: How to deploy Azure File Sync deploy a Storage Sync Service (A szinkronizálási szolgáltatások üzembe helyezése). 
- Győződjön meg arról, hogy a szinkronizálási csoport telepítve van. Ismerje meg, hogyan [hozhat létre szinkronizálási csoportot.](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint)
- Győződjön meg arról, hogy a kiszolgáló csatlakozik az internethez, és hogy az Azure elérhető. A kiszolgáló és a szolgáltatás közötti kommunikációhoz a 443-as portot használjuk.

## <a name="add-a-server-endpoint"></a>Kiszolgálói végpont felvétele
Kiszolgálóvégpont hozzáadásához lépjen a kívánt szinkronizálási csoporthoz, és válassza a "Kiszolgálói végpont hozzáadása" lehetőséget.

![Új kiszolgálói végpontok hozzáadása a szinkronizálási csoport panelen](media/storage-sync-files-server-endpoint/add-server-endpoint.png)

A kiszolgálóvégpont hozzáadása alatt a következő **információkra van szükség:**

- **Regisztrált kiszolgáló:** Annak a kiszolgálónak vagy fürtnek a neve, amely számára a kiszolgálóvégpontot létre kell hozni.
- **Elérési** út: A szinkronizálási csoport részeként szinkronizálni kívánó Windows Server elérési útja.
- **Felhőbeli rétegezés:** A felhőbeli rétegezés engedélyezésére vagy letiltására való váltás. Ha engedélyezve van, a felhőbeli rétegezés *rétege a* fájlokat az Azure-fájlmegosztások között fogja tartalmazni. Ez a helyszíni fájlmegosztásokat gyorsítótárba konvertálja az adatkészlet teljes másolata helyett, hogy segítsen a kiszolgáló helyhatékonyságának kezelésében.
- **Szabad kötetterület:** a kiszolgálóvégpontot tároló köteten lefoglalni szükséges szabad terület. Ha például a köteten a szabad terület 50%-ra van beállítva egy egyetlen kiszolgálóvégpontot tároló köteten, akkor az adatok mennyiségének körülbelül fele lesz Azure Files. Függetlenül attól, hogy a felhőbeli rétegezés engedélyezve van-e, az Azure-fájlmegosztás mindig rendelkezik a szinkronizálási csoportban található adatok teljes másolattal.

A **kiszolgálóvégpont** hozzáadásához válassza a Létrehozás lehetőséget. A szinkronizálási csoport névterében lévő fájlok mostantól szinkronban maradnak. 

## <a name="remove-a-server-endpoint"></a>Kiszolgálóvégpont eltávolítása
Ha szeretné megszüntetni a Azure File Sync egy adott kiszolgálóvégponthoz való használatát, eltávolíthatja a kiszolgálóvégpontot. 

> [!Warning]  
> Ne kísérelje meg a szinkronizálással, a felhőbeli rétegezéssel vagy a Azure File Sync egyéb aspektusával kapcsolatos hibák elhárítását a kiszolgálóvégpont eltávolításával és újralétrehozásával, kivéve, ha a Microsoft mérnöke kifejezetten erre utasítja. A kiszolgálóvégpont eltávolítása kioltó művelet, és a kiszolgálóvégponton belüli rétegzett fájlok nem lesznek "újracsatlakozva" az Azure-fájlmegosztásban lévő helyükről a kiszolgálóvégpont újbóli létrehozása után, ami szinkronizálási hibákat eredményez. Azt is vegye figyelembe, hogy a kiszolgálóvégpont-névtéren kívül található rétegzett fájlok véglegesen elveszhetnek. A rétegzett fájlok akkor is létezhetnek a kiszolgálóvégponton, ha a felhőbeli rétegezés soha nem volt engedélyezve.

Annak érdekében, hogy a kiszolgálóvégpont eltávolítása előtt a rendszer minden rétegzett fájlt visszahívjon, tiltsa le a felhőbeli rétegezést a kiszolgálóvégponton, majd hajtsa végre a következő PowerShell-parancsmagot a kiszolgálóvégpont-névtéren belüli összes rétegzett fájl visszahívásához:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
A `-Order CloudTieringPolicy` megadásakor a rendszer először a legutóbb módosított fájlokat fogja visszahívni.
Egyéb választható, de hasznos paraméterek:
* `-ThreadCount` A azt határozza meg, hogy hány fájl visszahívható párhuzamosan.
* `-PerFileRetryCount`meghatározza, hogy a rendszer milyen gyakran kísérelje meg a jelenleg blokkolt fájl visszahívási kísérletét.
* `-PerFileRetryDelaySeconds`A meghatározza az újrapróbálkozási kísérletek közötti időt másodpercben, és mindig az előző paraméterrel együtt kell használni.

> [!Note]  
> Ha a kiszolgálót üzemeltető helyi köteten nincs elegendő szabad terület az összes rétegzett adat visszahívása érdekében, a parancsmag sikertelen `Invoke-StorageSyncFileRecall` lesz.  

A kiszolgálóvégpont eltávolítása:

1. Lépjen arra a Társzinkronizálási szolgáltatásra, ahol a kiszolgáló regisztrálva van.
1. Keresse meg a kívánt szinkronizálási csoportot.
1. Távolítsa el a szinkronizálási csoportban a szinkronizálási szolgáltatásban a megfelelő kiszolgálóvégpontot. Ehhez kattintson a jobb gombbal a megfelelő kiszolgálóvégpontra a szinkronizálási csoport panelen.

    ![Kiszolgálóvégpont eltávolítása szinkronizálási csoportból](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Következő lépések
- [Kiszolgáló regisztrálása vagy regisztrációjának Azure File Sync](file-sync-server-registration.md)
- [Az Azure File Sync üzembe helyezésének megtervezése](file-sync-planning.md)
- [Az Azure File Sync monitorozása](file-sync-monitoring.md)
