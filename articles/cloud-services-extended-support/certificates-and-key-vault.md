---
title: Tanúsítványok tárolása és használata az Azure Cloud Services (kiterjesztett támogatás) szolgáltatásban
description: A tanúsítványok Azure Cloud Servicesban történő tárolásához és használatához szükséges folyamatok (kiterjesztett támogatás)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: d9ff86eeb0e64e7edbad0eeca51b04cabbd191e9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722653"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>Tanúsítványok használata az Azure Cloud Services (bővített támogatás)

A Key Vault a Cloud Serviceshoz társított tanúsítványok (kiterjesztett támogatás) tárolására szolgál. A kulcstartók a [Azure Portal](../key-vault/general/quick-create-portal.md) és a [PowerShell](../key-vault/general/quick-create-powershell.md)használatával hozhatók létre. Adja hozzá a tanúsítványokat a Key Vaulthoz, majd hivatkozzon a tanúsítvány ujjlenyomatai megfelelnek a szolgáltatás konfigurációs fájljában. Emellett engedélyeznie kell Key Vault a megfelelő engedélyekhez, hogy a Cloud Services (bővített támogatás) erőforrás beolvassa a titkos kulcsként tárolt tanúsítványt Key Vault.  

## <a name="upload-a-certificate-to-key-vault"></a>Tanúsítvány feltöltése Key Vault 

1.  Jelentkezzen be a Azure Portalba, és navigáljon a Key Vault. Ha nem rendelkezik Key Vault beállítással, dönthet úgy, hogy ugyanabban az ablakban létrehoz egyet.

2. **Hozzáférési házirendek** kiválasztása

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="A rendszerkép a Key Vault paneljén található hozzáférési szabályzatok kiválasztását mutatja be.":::

3. Győződjön meg arról, hogy a hozzáférési házirendek a következő tulajdonságokat tartalmazzák:
    - **Azure-Virtual Machines való hozzáférés engedélyezése az üzembe helyezéshez**
    - **Azure Resource Manager hozzáférésének engedélyezése a sablonok üzembe helyezéséhez** 

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="A rendszerkép a Azure Portal hozzáférési szabályzatok ablakát jeleníti meg.":::
 
4.  **Tanúsítványok** kiválasztása 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="A rendszerkép a tanúsítványok lehetőség kiválasztását mutatja a Azure Portal Key Vault panel házirendjei ablakában.":::

5. **Előállítási/importálási** lehetőség kiválasztása

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="A rendszerkép megjeleníti a generált/importálás lehetőséget.":::

4.  A tanúsítvány feltöltésének befejezéséhez végezze el a szükséges információkat. 

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="A képen a Azure Portal importálás ablaka látható.":::

5.  Adja hozzá a tanúsítvány részleteit a szerepkörhöz a szolgáltatás konfigurációs (. cscfg) fájljában. Győződjön meg arról, hogy a Azure Portal található tanúsítvány ujjlenyomata megegyezik a szolgáltatás konfigurációs (. cscfg) fájljának ujjlenyomatával. 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```

## <a name="next-steps"></a>Következő lépések 
- Tekintse át a Cloud Services [üzembe helyezésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás).
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).