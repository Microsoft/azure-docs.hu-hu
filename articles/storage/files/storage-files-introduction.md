---
title: Bevezetés az Azure Files használatába | Microsoft Docs
description: Az Azure Files szolgáltatás áttekintése, amely lehetővé teszi hálózati fájlmegosztások létrehozását és használatát a felhőben az iparági szabvány SMB protokoll használatával.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0dcd763240205bd396fc8cd0301c2046098473b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98070133"
---
# <a name="what-is-azure-files"></a>Mi az Azure Files?
A Azure Files teljes körűen felügyelt fájlmegosztást biztosít a felhőben, amely az iparági szabványnak megfelelő [SMB protokoll](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) vagy a [hálózati fájlrendszer (NFS) protokollon](https://en.wikipedia.org/wiki/Network_File_System)keresztül érhető el. Az Azure-fájlmegosztást a felhő vagy a helyszíni üzemelő példányok párhuzamosan is csatlakoztathatja. Azure Files SMB-fájlmegosztás elérhető Windows, Linux és macOS rendszerű ügyfelekről. Azure Files NFS-fájlmegosztás elérhető Linux vagy macOS rendszerű ügyfelekről. Emellett Azure Files az SMB-fájlmegosztás gyorsítótárazása a Windows-kiszolgálókon Azure File Sync a gyors elérés érdekében, ahol az adatforgalom használatban van.

## <a name="videos"></a>Videók
| Azure File Sync bemutatása | Azure Files szinkronizálással (Ignite 2019)  |
|-|-|
| [![Képernyőfelvétel a bevezetéséről Azure File Sync videó – kattintson a lejátszáshoz!](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Képernyőfelvétel a Azure Files szinkronizálási bemutatóval – kattintson a lejátszáshoz!](./media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

Íme néhány videó a Azure Files gyakori használati eseteiről:
* [A fájlkiszolgáló cseréje kiszolgáló nélküli Azure-fájlmegosztás esetén](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
* [Ismerkedés a FSLogix-profilok tárolókkal Azure Files a Windows rendszerű virtuális asztalon az AD-hitelesítés használatával](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="why-azure-files-is-useful"></a>Miért hasznos az Azure Files?
Az Azure-fájlmegosztások az alábbiakra használhatók:

* **Helyszíni fájlkiszolgálók lecserélése vagy kiegészítése**:  
    Az Azure Files használatával teljes mértékben lecserélheti vagy kiegészítheti a hagyományos helyszíni fájlkiszolgálókat vagy NAS-eszközöket. Az Azure-fájlmegosztások közvetlenül csatlakoztathatók a népszerű operációs rendszerekhez (például Windows, macOS és Linux) a földrajzi helyüktől függetlenül. Az Azure file SMB-fájlmegosztás Azure File Sync is replikálható a Windows-kiszolgálókkal a helyszíni vagy a felhőben, a teljesítményre és az elosztott gyorsítótárazásra a használatban lévő adat alapján. Az [Azure Files ad-hitelesítés](storage-files-active-directory-overview.md)legújabb kiadásával az Azure file SMB-fájlmegosztás továbbra is képes együttműködni a helyszíni Active Directoryval a hozzáférés-vezérléshez. 

* **A "lift and SHIFT" alkalmazások**:  
    Az Azure Files segítségével könnyedén „átemelhetők” azok az alkalmazások a felhőbe, amelyekhez fájlmegosztás szükséges a fájlalkalmazások és a felhasználók adatainak tárolásához. Az Azure Files az alkalmazást és az adatokat egyaránt az Azure-ba áthelyező „klasszikus”, és az adatokat az Azure Filesba áthelyező és az alkalmazást továbbra is a helyszíni környezetben futtató „hibrid” átemelési forgatókönyvet is támogatja. 

* A **felhőalapú fejlesztés egyszerűsítése**:  
    Az Azure Files emellett számos különféle módon segítheti az új felhőalapú fejlesztési projektek egyszerűbbé tételét. Például:
    * **Megosztott alkalmazás beállításai**:  
        Az elosztott alkalmazások egyik jellegzetessége, hogy egy központi helyen található a konfigurációs fájljuk, ahol számos alkalmazáspéldány hozzáférhet. Az alkalmazáspéldányok a konfigurációjukat a Fájl REST API-val tudják betölteni, és a felhasználók az SMB-megosztások helyi csatlakoztatásával érhetik el őket igény szerint.

    * **Diagnosztikai megosztás**:  
        Az Azure-fájlmegosztások kényelmes helyet biztosítanak a felhőalkalmazások számára a naplók, a mérőszámok és az összeomlási memóriaképek írásához. Az alkalmazáspéldányok a Fájl REST API-val írhatják a naplókat, a fejlesztők pedig a fájlmegosztás a helyi gépükön való csatlakoztatásával érhetik el őket. Ez nagy rugalmasságot biztosít, mivel a fejlesztők anélkül használhatják ki a felhő előnyeit, hogy le kellene mondaniuk az általuk használt és kedvelt eszközökről.

    * Fejlesztés **/tesztelés/hibakeresés**:  
        Amikor a fejlesztők vagy rendszergazdák a felhőben végeznek munkálatokat a virtuális gépen, gyakran van szükségük ugyanazokra az eszközökre vagy segédprogramokra. Az ilyen segédprogramok és eszközök az egyes virtuális gépekre másolása időigényes feladat. Az Azure-fájlmegosztások a virtuális gépekre történő helyi csatlakoztatásával a fejlesztők és a rendszergazdák gyorsan hozzáférhetnek eszközeikhez és segédprogramjaikhoz másolás nélkül.
* **Tárolókra bontás**:  
    Az Azure-fájlmegosztás az állapot-nyilvántartó tárolók állandó köteteiként használható. A tárolók a "Build Once, bárhol" funkciót biztosítják, amelyek lehetővé teszik a fejlesztők számára az innováció felgyorsítását. Ahhoz, hogy a tárolók minden indításkor hozzáférjenek a nyers adatszolgáltatáshoz, egy megosztott fájlrendszerre van szükség ahhoz, hogy a tárolók hozzáférhessenek a fájlrendszerhez, függetlenül attól, hogy melyik példányt futnak.

## <a name="key-benefits"></a>Főbb előnyök
* **Megosztott hozzáférés**. Az Azure-fájlmegosztás támogatja az iparági szabványnak megfelelő SMB-és NFS-protokollokat, ami azt jelenti, hogy zökkenőmentesen lecserélheti a helyszíni fájlmegosztást az Azure-fájlmegosztás használatával anélkül, hogy az alkalmazások kompatibilitásával kellene foglalkoznia. A fájlrendszerek több gépen, alkalmazásban/példányban történő megosztásának lehetősége az Azure Files egyik jelentős előnye a megoszthatóságot igénylő alkalmazások esetében. 
* **Teljes mértékben felügyelt**. Az Azure-fájlmegosztások anélkül is létrehozhatók, hogy felügyelnie kellene a hardvereket vagy az operációs rendszert. Ez azt jelenti, hogy nincs szükség a kiszolgáló operációs rendszerének kritikus fontosságú frissítésekkel történő javítására vagy a hibás merevlemezek cseréjére.
* **Parancsfájlok és eszközök**. A PowerShell-parancsmagok és az Azure CLI használatával az Azure-fájlmegosztás az Azure-alkalmazások felügyeletének részeként hozható létre, csatlakoztatható és kezelhető. Az Azure-fájlmegosztás Azure Portal és Azure Storage Explorer használatával hozható létre és kezelhető. 
* **Rugalmasság**. Az Azure Files szolgáltatást a kezdetektől úgy hozták létre, hogy folyamatosan rendelkezésre álljon. Ha lecseréli helyszíni fájlmegosztásait az Azure Filesra, akkor többé nem kell helyi áramkimaradásokkal vagy hálózati hibákkal megküzdenie. 
* **Ismerős programozhatóság**. Az Azure-ban futó alkalmazások a [fájlrendszer adatátviteli API-jain](/dotnet/api/system.io.file) keresztül férhetnek hozzá a megosztás adataihoz. A fejlesztők épp ezért meglévő kódjaik és képességeik felhasználásával áttelepíthetik az alkalmazásokat. A rendszer átviteli API-jai mellett az [Azure Storage ügyfélkódtárait](/previous-versions/azure/dn261237(v=azure.100)) vagy az [Azure Storage REST API-ját](/rest/api/storageservices/file-service-rest-api) is használhatja.

## <a name="next-steps"></a>Következő lépések
* [További információ a rendelkezésre álló fájlmegosztási protokollokról](storage-files-compare-protocols.md)
* [Azure-fájlmegosztás létrehozása](storage-how-to-create-file-share.md)
* [SMB-megosztás csatlakoztatása és csatlakoztatása Windows rendszeren](storage-how-to-use-files-windows.md)
* [SMB-megosztás csatlakoztatása és csatlakoztatása Linux rendszeren](storage-how-to-use-files-linux.md)
* [SMB-megosztás csatlakoztatása és csatlakoztatása macOS-en](storage-how-to-use-files-mac.md)
* [NFS-megosztás létrehozása](storage-files-how-to-create-nfs-shares.md)
