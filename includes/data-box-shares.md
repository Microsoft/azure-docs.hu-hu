---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/05/2020
ms.author: alkohli
ms.openlocfilehash: 5aaf0ce747b14b2fa9f2fcd9a65b774aa7d2db3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87102721"
---
A kiválasztott tárfióktól függően a Data Box a következőket hozhatja létre:

* Három megosztás minden társított tárfiókhoz, GPv1-hez és GPv2-höz.
* Egy megosztás a prémium szintű Storage-hoz.
* Egy megosztás a Blob Storage-fiókhoz.

A blokkblob- és lapblobmegosztások alatti első szintű entitások tárolók, a második szintű entitások pedig blobok. Az Azure Files-megosztások alatti első szintű entitások megosztások, a második szintű entitások pedig fájlok.

Az alábbi táblázat a Data Boxon található megosztások UNC elérési útját és az adatok feltöltéséhez használt Azure Storage elérési útjának URL-címét mutatja. Az Azure Storage elérési útjának végső URL-címe a megosztás UNC elérési útjából származik.
 
| Blobok és fájlok | Elérési utak és URL-címek |
| --------------- | -------------- |
| Azure-blokkblobok | <li>A megosztások UNC elérési útja: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-lapblobok  | <li>A megosztások UNC elérési útja: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>A megosztások UNC elérési útja: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

