---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 80411ac191d0385ddafd4a3cc490d322ce039689
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91642428"
---
- Ne másolja a fájlokat közvetlenül az előlétrehozott megosztások egyikére. Létre kell hoznia egy mappát a megosztás alatt, majd át kell másolnia a fájlokat a mappába.
- A *StorageAccount_BlockBlob* és *StorageAccount_PageBlob* nevű mappa egy tároló. A tárolók például *StorageAccount_BlockBlob/Container* és *StorageAccount_PageBlob/Container*jönnek létre.
- A közvetlenül a *StorageAccount_AzureFiles* alatt létrehozott mappákat egy Azure-fájlmegosztás fordítja le.
- Ha egy másolandó objektum neve megegyezik egy olyan Azure-objektummal, mint például egy blob vagy egy olyan fájl, amely már a felhőben található, Data Box felülírja a fájlt a felhőben.
- A rendszer a *StorageAccount_BlockBlobba* írt összes fájlt és *StorageAccount_PageBlob* -megosztást a blob és az oldal blobként feltölti.
- Az Azure Blob Storage nem támogatja a címtárakat. Ha a *StorageAccount_BlockBlob* mappában hoz létre mappát, a virtuális mappák a blob nevében jönnek létre. Azure Files esetén a rendszer karbantartja a tényleges címtár-struktúrát.
- A *StorageAccount_BlockBlob* és *StorageAccount_PageBlob* -mappákban létrehozott üres címtár-hierarchiák nincsenek feltöltve.
- Ha bármilyen hiba merül fel az Azure-ba történő adatfeltöltés során, a rendszer egy naplófájlt hoz létre a célként szolgáló Storage-fiókban. A hibanapló elérési útja a feltöltés befejezésekor érhető el, és áttekintheti a naplót a javítási műveletek elvégzéséhez. Ne töröljön adatok a forrásból a feltöltött adatok ellenőrzése nélkül.
- A fájl-metaadatok és az NTFS-engedélyek megtekinthetők, ha az adatokat a rendszer a [fájl ACL-ek, attribútumaik és időbélyegek megőrzésére szolgáló](../articles/databox/data-box-file-acls-preservation.md)útmutatás használatával Azure Files feltölti Azure Data Box.