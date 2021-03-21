---
ms.service: hpc-cache
ms.topic: include
ms.date: 03/15/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 2804b3bb36a31c9cfbf4cce8db94b7469c8975c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563239"
---
| Használati modell | Gyorsítótárazási mód | Háttér-ellenőrzés | Maximális írási késleltetés |
|--|--|--|--|
| Súlyos, ritka írások olvasása | Olvasás | Soha | Nincsenek |
| 15%-nál nagyobb írások | Olvasás/írás | 8 óra | 20 perc |
| Az ügyfelek megkerülik a gyorsítótárat | Olvasás | 30 másodperc | Nincsenek |
| 15%-nál nagyobb írások, gyakori háttér-ellenőrzés (30 másodperc) | Olvasás/írás | 30 másodperc | 20 perc |
| 15%-nál nagyobb írások, gyakori háttér-ellenőrzés (60 másodperc) | Olvasás/írás | 60 másodperc | 20 perc |
| 15%-nál nagyobb írások, gyakori visszaírások | Olvasás/írás | 30 másodperc | 30 másodperc |
| Nagy mennyiségű, a kiszolgáló biztonsági mentése 3 óránként | Olvasás | 3 óra | Nincsenek |
