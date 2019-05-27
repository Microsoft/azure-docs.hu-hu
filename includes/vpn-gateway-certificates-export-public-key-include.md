---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/13/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: afd4836229c60ebef1536d4fa1ca4206a492e56d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150322"
---
Önaláírt főtanúsítvány létrehozása után exportálja a főtanúsítvány nyilvános kulcsú .cer fájlját (ne a titkos kulcsot). Később fogja feltölteni ezt a fájlt az Azure-bA. A következő lépések segítségével exportálhatja a az önaláírt főtanúsítványhoz tartozó .cer fájlt:

1. A .cer fájl tanúsítványból történő beszerzéséhez nyissa meg a **Felhasználói tanúsítványok kezelése** elemet. Keresse meg az önaláírt főtanúsítványt. Ezt általában a „Tanúsítványok – aktuális felhasználó\Személyes\Tanúsítványok” útvonalon érheti el. Ha megtalálta, kattintson rá a jobb egérgombbal. Kattintson a **Minden feladat**, majd az **Exportálás** elemre. Megnyílik a **Tanúsítványexportáló varázsló**. Ha nem találja a tanúsítványt aktuális felhasználó\személyes\tanúsítványok útvonalon, előfordulhat, hogy véletlenül nyitotta "Tanúsítványok – helyi számítógép", "Tanúsítványok – aktuális felhasználó" helyett). Ha meg szeretné nyitni a Certificate Manager PowerShell-lel, adja meg jelenlegi felhasználó hatókörében *certmgr* a konzolablakban.

   ![Exportálás](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. A varázslóban kattintson a **Tovább** gombra.

   ![Tanúsítvány exportálása](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Válassza a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Nem akarom exportálni a titkos kulcs](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Az **Exportfájlformátum** lapon válassza a **Base-64 kódolású X.509 (.CER)** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Base-64 kódolású](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. A **exportálandó fájl**, **Tallózás** arra a helyre, amelyhez exportálja a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Tovább** gombra.

   ![Tallózás](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

   ![Befejezés](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. A tanúsítvány exportálása sikeresen megtörtént.

   ![Siker](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Az exportált tanúsítványt ehhez hasonlóan néz ki:

   ![Exportálva](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Nyissa meg a Jegyzettömbben az exportált tanúsítványt, ha valami hasonló ebben a példában láthatja. A kék szakaszában az Azure-bA feltöltött adatokat tartalmazza. A Jegyzettömb alkalmazásban nyissa meg a tanúsítványt, és nem tűnik ehhez hasonló, általában ez azt jelenti, hogy nem exportált, használja a Base-64 kódolású X.509 (. CER) formátumban. Emellett ha egy másik szöveges szerkesztő használni kívánt, ismerje meg, hogy egyes szerkesztők megjelentetni nem kívánt formázása a háttérben. Ez problémákat, ha ezt a tanúsítványt a szöveget az Azure-bA feltöltött hozhat létre.

   ![Nyissa meg a Jegyzettömbben](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
