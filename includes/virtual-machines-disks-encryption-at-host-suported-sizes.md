---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e8ec7faf1562381288aeef630bf2076ce413017a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531512"
---
A virtuálisgép-méretek legújabb generációja támogatja a gazdagépen való titkosítást:

|Típus  |Nem támogatott  |Támogatott  |
|---------|---------|---------|
|Általános célú     | Dv3, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dav4, Dasv4        |
|Számításra optimalizált     |         | Fsv2        |
|Memóriaoptimalizált     | Ev3        | DSv2, Esv3, M, Mv2, Eav4, Easv4        |
|Tárolásra optimalizált     |         | Ls, Lsv2 (nincs titkosítva NVMe-lemez)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (előzetes verzió)        |
|Nagy teljesítményű számítás     | H        | HB, HC, HBv2        |
|Előző generációk     | F, A, D, L, G        | DS, GS, Fs, NVv2        |
