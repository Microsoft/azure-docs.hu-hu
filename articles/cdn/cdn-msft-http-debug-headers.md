---
title: HTTP-fejlécek hibakeresése Azure CDN a Microsofttól | Microsoft Docs
description: A hibakeresési gyorsítótár-kérelmek fejlécei további információkat nyújtanak a kért eszközre alkalmazott gyorsítótár-házirendről. Ezek a fejlécek kifejezetten a Microsoft Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 2475bdce3ab8f153cc837601964bf4a2e90a470c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81260411"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Hibakeresés HTTP-fejléce Azure CDN a Microsofttól
A hibakeresési válasz fejléce `X-Cache` részletesen ismerteti, hogy a CDN-verem melyik rétegét kézbesítették a tartalom. Ez a fejléc kifejezetten a Microsoft Azure CDN.

### <a name="response-header-format"></a>Válasz fejlécének formátuma

Fejléc | Leírás
-------|------------
X gyorsítótár: TCP_HIT | A rendszer ezt a fejlécet adja vissza, amikor a tartalmat a CDN Edge-gyorsítótárból kézbesíti. 
X gyorsítótár: TCP_REMOTE_HIT | A rendszer ezt a fejlécet adja vissza, amikor a tartalmat a CDN regionális gyorsítótárból (Origin Shield Layer) kézbesíti.
X gyorsítótár: TCP_MISS | A rendszer ezt a fejlécet adja vissza, ha a gyorsítótárból hiányzik a gyorsítótár, és a tartalom a forrástól származik. 


