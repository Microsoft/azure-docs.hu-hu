---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 018cf621c65e86877a76c9861c999caf67f3b8cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94553124"
---
Ügyféltanúsítvány létrehozásakor a rendszer automatikusan telepíti azt a számítógépre, amelyet a létrehozásához használt. Ha az ügyféltanúsítványt egy másik ügyfélszámítógépen szeretné telepíteni, exportálnia kell a létrehozott ügyféltanúsítványt.

1. Ügyféltanúsítvány exportálásához nyissa meg a **Felhasználói tanúsítványok kezelése** elemet. A létrehozott Ügyféltanúsítványok alapértelmezés szerint a "Certificates-current User\Personal\Certificates" mappában találhatók. Kattintson a jobb gombbal az exportálni kívánt ügyféltanúsítvány elemre, kattintson a **minden feladat**, majd az **Exportálás** elemre a **Tanúsítvány exportálása varázsló** megnyitásához.

   ![Képernyőfelvétel: az aktuális felhasználóhoz tartozó tanúsítványok ablak, amely a kijelölt tanúsítványokat és az összes tevékenység közül kiválasztott exportálást tartalmazza.](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. A tanúsítvány exportálása varázslóban kattintson a tovább **gombra a** folytatáshoz.

   ![A képernyőfelvételen a tanúsítvány exportálása varázsló üdvözlő üzenete látható.](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Válassza **az igen, a titkos kulcs exportálása** lehetőséget, majd kattintson a **tovább** gombra.

   ![titkos kulcs exportálása](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Az **Exportfájlformátum** lapon hagyja bejelölve az alapértelmezett elemeket. Győződjön meg róla, hogy a **Minden tanúsítvány belefoglalása a tanúsítványláncba** jelölőnégyzet be van jelölve. Ezzel a beállítással emellett exportálhatja a sikeres ügyfél-hitelesítéshez szükséges főtanúsítvány-információkat. Enélkül nem sikerül az ügyfél-hitelesítés, mert az ügyfél nem rendelkezik a megbízható főtanúsítványsal. Ezután kattintson a **tovább** gombra.

   ![Exportálás fájlformátuma](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. A **Biztonság** lapon be kell állítania a titkos kulcs védelmét. Ha jelszó használata mellett dönt, jegyezze fel vagy jegyezze meg a tanúsítványhoz beállított jelszót. Ezután kattintson a **tovább** gombra.

   ![Képernyőfelvétel: a tanúsítvány-Exportálás varázsló Biztonság lapja, amely a beírt és megerősített jelszóval jelenik meg.](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. Az **Exportálandó fájl** lapon a **Tallózás** gombra kattintva keresse meg azt a helyet, ahová exportálni szeretné a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **tovább** gombra.

   ![exportálandó fájl](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

   ![Képernyőfelvétel: a tanúsítvány exportálása varázsló, amely a megadott beállításokat tartalmazza.](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)