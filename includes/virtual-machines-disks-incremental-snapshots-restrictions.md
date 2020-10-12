---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4af8a150b062526f08c1d15581ec26e2fe12d8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102711"
---
- A növekményes Pillanatképek jelenleg nem helyezhetők át az előfizetések között.
- Jelenleg csak egy adott pillanatkép-családhoz tartozó SAS URI-k hozhatók létre akár öt pillanatképből.
- Nem hozható létre növekményes pillanatkép a lemez előfizetésén kívüli adott lemezhez.
- A lemezenként legfeljebb hét növekményes pillanatkép hozható létre 5 percenként.
- Egyetlen lemezhez összesen 200 növekményes pillanatkép hozható létre.
- Nem lehet lekérdezni a pillanatképek közötti módosításokat, és azt követően, hogy módosította a szülő lemez méretét 4 TB-os határon belül. Tegyük fel például, hogy egy növekményes pillanatkép-pillanatkép – a, ha a lemez mérete 2 TB volt. Most megnövelte a lemez méretét 6 TB-ra, majd újabb növekményes pillanatkép-pillanatképet (b) vett fel. A Snapshot-a és a Snapshot-b közötti módosításokat nem lehet lekérni. Újra le kell töltenie az átméretezés után létrehozott pillanatkép-b teljes másolatát. Ezt követően a Snapshot-b és a Snapshot-b után létrehozott Pillanatképek közötti változásokat is beolvashatja. 
