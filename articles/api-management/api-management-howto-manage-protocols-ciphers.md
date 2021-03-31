---
title: Protokollok és titkosítások kezelése az Azure API Managementban | Microsoft Docs
description: Ismerje meg, hogyan kezelhetők a protokollok (TLS) és a titkosítások (DES) az Azure API Managementban.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: 043a3d0b63dfc74f587b58b3c2ac42f1a084cc4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86250311"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Protokollok és titkosítások kezelése az Azure API Managementben

Az Azure API Management a TLS protokoll több verzióját is támogatja az ügyfél-és a háttérbeli, valamint a 3DES titkosításhoz.

Ez az útmutató bemutatja, hogyan kezelheti a protokollok és a titkosítási konfigurációk konfigurációját egy Azure API Management-példány esetében.

![Protokollok és titkosítások kezelése a APIM-ben](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következőket kell tennie:

* Egy API Management-példány

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>A TLS-protokollok és a 3DES titkosítás kezelése

1. Navigáljon a **API Management-példányhoz** a Azure Portal.
2. Válassza ki a **protokoll beállításait** a menüből.  
3. A kívánt protokollok vagy titkosítások engedélyezése vagy letiltása.
4. Kattintson a **Mentés** gombra. A módosítások egy órán belül lesznek alkalmazva.  

## <a name="next-steps"></a>Következő lépések

* További információ a [TLS-ről (Transport Layer Security)](/dotnet/framework/network-programming/tls).
* További [videók](https://azure.microsoft.com/documentation/videos/index/?services=api-management) a API Managementról.
