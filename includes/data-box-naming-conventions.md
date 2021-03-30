---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "66244630"
---
| Entitás                                       | Konvenciók   |
|----------------------------------------------|-----------------------------------------------------|
| Tárolók nevei a blob és az oldal blobja számára | Érvényes DNS-névnek kell lennie, amely 3 – 63 karakter hosszú lehet. <br>  Betűvel vagy számmal kell kezdődnie. <br> Csak kisbetűket, számokat és kötőjelet (-) tartalmazhat. <br> Minden kötőjel (-) előtt és után közvetlenül egy betűnek vagy számnak kell állnia. <br> Az egymást követő kötőjelek nem engedélyezettek a névben. |
| Az Azure Files-nevek megosztása                  | Lásd fent                                          |
| Az Azure Files címtár-és fájlnevei     |<li> Kis-és nagybetűket megőrző, kis-és nagybetűk megkülönböztetése, és legfeljebb 255 karakter hosszú lehet. </li><li> A perjel (/) nem végződhet. </li><li>Ha meg van adni, a rendszer automatikusan eltávolítja. </li><li> A következő karakterek nem engedélyezettek: <code>" \\ / : \| < > * ?</code></li><li> A fenntartott URL-karaktereket escape-karakterrel kell jelölni. </li><li> A nem engedélyezett URL-címek elérési útja érvénytelen. A kódok, például a \\ uE000 nem érvényes Unicode-karakterek. Bizonyos ASCII-vagy Unicode-karakterek, például a vezérlési karakterek (kell lennie 0x00 – 0x1F, \\ u0081 stb.) nem engedélyezettek. A Unicode karakterláncokat a HTTP/1.1-es verzióra vonatkozó szabályoknál lásd: RFC 2616, 2,2. szakasz, alapszintű szabályok és RFC 3987. </li><li> A következő fájlnevek nem engedélyezettek: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK $, dot Character (.), és két dot karakter (...).</li>|
| Blobnevek blokkblob és lapblob esetén      | </li><li>A blobnevek megkülönböztetik a kis- és nagybetűket, illetve bármilyen karakterkombinációt tartalmazhatnak </li><li>A blob nevének 1–1024 karakter hosszúságúnak kell lennie. </li><li>A fenntartott URL-karaktereket escape-karakterrel kell jelölni. </li><li>A blob nevét alkotó részleges útvonalak száma legfeljebb 254 lehet. A részleges útvonalak két elválasztókarakter (pl. a perjel „/”) közötti sztringek, amelyek megegyeznek egy virtuális könyvtár nevével.</li> |
