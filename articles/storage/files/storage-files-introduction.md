---
title: Bevezetés az Azure Files használatába | Microsoft Docs
description: Az Azure Files szolgáltatás áttekintése, amely lehetővé teszi hálózati fájlmegosztások létrehozását és használatát a felhőben az iparági szabvány SMB protokoll használatával.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7279c5578d969db9178ce85734e3a43f9eccdf8d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780046"
---
# <a name="what-is-azure-files"></a>Mi az Azure Files?
Azure Files felhőben teljes körűen felügyelt fájlmegosztásokat kínál, amelyek az iparági szabvány [Server Message Block (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) protokollon vagy a hálózati fájlrendszer [(NFS) protokollon keresztül érhetők el.](https://en.wikipedia.org/wiki/Network_File_System) Az Azure-fájlmegosztások párhuzamosan csatlakoztathatóak felhőalapú vagy helyszíni üzemelő példányok által. Azure Files SMB-fájlmegosztások Windows-, Linux- és macOS-ügyfelekről érhetők el. Azure Files NFS-fájlmegosztások Linux- vagy macOS-ügyfelekről érhetők el. Emellett az Azure Files SMB-fájlmegosztások gyorsítótárazhatóak Azure File Sync windowsos kiszolgálókon az adatok helyéhez közeli gyors elérés érdekében.

Íme néhány videó a leggyakoribb Azure Files:
* [A fájlkiszolgáló cseréje kiszolgáló nélküli Azure-fájlmegosztásra](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
* [FSLogix-profiltárolók az AD-Azure Files Windows Virtual Desktop való használatával – első lépések](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="why-azure-files-is-useful"></a>Miért hasznos az Azure Files?
Az Azure-fájlmegosztások az alábbiakra használhatók:

* **Helyszíni fájlkiszolgálók lecserélése vagy kiegészítése**:  
    Az Azure Files használatával teljes mértékben lecserélheti vagy kiegészítheti a hagyományos helyszíni fájlkiszolgálókat vagy NAS-eszközöket. Az Azure-fájlmegosztások közvetlenül csatlakoztathatók a népszerű operációs rendszerekhez (például Windows, macOS és Linux) a földrajzi helyüktől függetlenül. Az Azure File SMB-fájlmegosztások az Azure File Sync-val is replikálhatók helyszíni vagy felhőbeli Windows-kiszolgálókra az adatok a használat helyéhez használt adatok teljesítményének és elosztott gyorsítótárazása érdekében. Az [AD-Azure Files](storage-files-active-directory-overview.md)kiadásával az Azure File SMB-fájlmegosztások továbbra is használhatók a helyszínen üzemeltetett AD-val a hozzáférés-vezérlés érdekében. 

* **"Átemlhető" alkalmazások:**  
    Az Azure Files segítségével könnyedén „átemelhetők” azok az alkalmazások a felhőbe, amelyekhez fájlmegosztás szükséges a fájlalkalmazások és a felhasználók adatainak tárolásához. Az Azure Files az alkalmazást és az adatokat egyaránt az Azure-ba áthelyező „klasszikus”, és az adatokat az Azure Filesba áthelyező és az alkalmazást továbbra is a helyszíni környezetben futtató „hibrid” átemelési forgatókönyvet is támogatja. 

* **A felhőalapú fejlesztés egyszerűsítése:**  
    Az Azure Files emellett számos különféle módon segítheti az új felhőalapú fejlesztési projektek egyszerűbbé tételét. Például:
    * **Megosztott alkalmazásbeállítások:**  
        Az elosztott alkalmazások egyik jellegzetessége, hogy egy központi helyen található a konfigurációs fájljuk, ahol számos alkalmazáspéldány hozzáférhet. Az alkalmazáspéldányok a konfigurációjukat a Fájl REST API-val tudják betölteni, és a felhasználók az SMB-megosztások helyi csatlakoztatásával érhetik el őket igény szerint.

    * **Diagnosztikai megosztás:**  
        Az Azure-fájlmegosztások kényelmes helyet biztosítanak a felhőalkalmazások számára a naplók, a mérőszámok és az összeomlási memóriaképek írásához. Az alkalmazáspéldányok a Fájl REST API-val írhatják a naplókat, a fejlesztők pedig a fájlmegosztás a helyi gépükön való csatlakoztatásával érhetik el őket. Ez nagy rugalmasságot biztosít, mivel a fejlesztők anélkül használhatják ki a felhő előnyeit, hogy le kellene mondaniuk az általuk használt és kedvelt eszközökről.

    * **Dev/Test/Debug**:  
        Amikor a fejlesztők vagy rendszergazdák a felhőben végeznek munkálatokat a virtuális gépen, gyakran van szükségük ugyanazokra az eszközökre vagy segédprogramokra. Az ilyen segédprogramok és eszközök az egyes virtuális gépekre másolása időigényes feladat. Az Azure-fájlmegosztások a virtuális gépekre történő helyi csatlakoztatásával a fejlesztők és a rendszergazdák gyorsan hozzáférhetnek eszközeikhez és segédprogramjaikhoz másolás nélkül.
* **Tárolóba ásás:**  
    Az Azure-fájlmegosztások tartós kötetekként használhatók állapot-alapú tárolókhoz. A tárolók "egyszer, bárhol futtatva" kínálnak olyan képességeket, amelyek lehetővé teszik a fejlesztők számára az innováció felgyorsítát. A nyers adatokhoz minden indításkor hozzáférő tárolók esetén megosztott fájlrendszerre van szükség ahhoz, hogy ezek a tárolók hozzáférnek a fájlrendszerhez, függetlenül attól, hogy melyik példányon futnak.

## <a name="key-benefits"></a>Főbb előnyök
* **Megosztott hozzáférés**. Az Azure-fájlmegosztások támogatják az iparági szabvány SMB- és NFS-protokollokat, ami azt jelenti, hogy zökkenőmentesen lecserélheti a helyszíni fájlmegosztásokat Azure-fájlmegosztásokra anélkül, hogy aggódnia kellene az alkalmazáskompatibilitás miatt. A fájlrendszerek több gépen, alkalmazásban/példányban történő megosztásának lehetősége az Azure Files egyik jelentős előnye a megoszthatóságot igénylő alkalmazások esetében. 
* **Teljes körűen felügyelt.** Az Azure-fájlmegosztások anélkül is létrehozhatók, hogy felügyelnie kellene a hardvereket vagy az operációs rendszert. Ez azt jelenti, hogy nincs szükség a kiszolgáló operációs rendszerének kritikus fontosságú frissítésekkel történő javítására vagy a hibás merevlemezek cseréjére.
* **Szkriptek és eszközök.** A PowerShell-parancsmagok és az Azure CLI használatával Azure-fájlmegosztásokat hozhat létre, csatlakoztathat és kezelhet az Azure-alkalmazások felügyeletének részeként. Azure-fájlmegosztásokat hozhat létre és kezelhet Azure Portal és Azure Storage Explorer. 
* **Rugalmasság.** Az Azure Files szolgáltatást a kezdetektől úgy hozták létre, hogy folyamatosan rendelkezésre álljon. Ha lecseréli helyszíni fájlmegosztásait az Azure Filesra, akkor többé nem kell helyi áramkimaradásokkal vagy hálózati hibákkal megküzdenie. 
* **Ismerős programozhatóság.** Az Azure-ban futó alkalmazások a [fájlrendszer adatátviteli API-jain](/dotnet/api/system.io.file) keresztül férhetnek hozzá a megosztás adataihoz. A fejlesztők épp ezért meglévő kódjaik és képességeik felhasználásával áttelepíthetik az alkalmazásokat. A rendszer átviteli API-jai mellett az [Azure Storage ügyfélkódtárait](/previous-versions/azure/dn261237(v=azure.100)) vagy az [Azure Storage REST API-ját](/rest/api/storageservices/file-service-rest-api) is használhatja.

## <a name="next-steps"></a>Következő lépések
* [További információ az elérhető fájlmegosztási protokollról](storage-files-compare-protocols.md)
* [Azure-fájlmegosztás létrehozása](storage-how-to-create-file-share.md)
* [SMB-megosztás csatlakoztatása Windows rendszeren](storage-how-to-use-files-windows.md)
* [SMB-megosztás csatlakoztatása Linux rendszeren](storage-how-to-use-files-linux.md)
* [SMB-megosztás csatlakoztatása macOS rendszeren](storage-how-to-use-files-mac.md)
* [NFS-megosztás létrehozása](storage-files-how-to-create-nfs-shares.md)
