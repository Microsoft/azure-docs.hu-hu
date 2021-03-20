---
title: Azure Cloud Service üzembe helyezése (bővített támogatás) – Azure Portal
description: Azure Cloud Service üzembe helyezése (kiterjesztett támogatás) a Azure Portal használatával
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9c1abe1323bc095fe4dfbfc559ef7e159d1f7532
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880675"
---
# <a name="deploy-a-azure-cloud-services-extended-support-using-the-azure-portal"></a>Azure Cloud Services (bővített támogatás) üzembe helyezése a Azure Portal használatával
Ez a cikk azt ismerteti, hogyan használható a Azure Portal a Cloud Service (bővített támogatás) központi telepítésének létrehozásához. 

> [!IMPORTANT]
> A Cloud Services (bővített támogatás) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Előkészületek

Tekintse át a Cloud Services [telepítésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás), és hozza létre a kapcsolódó erőforrásokat. 

## <a name="deploy-a-cloud-services-extended-support"></a>Cloud Services üzembe helyezése (kiterjesztett támogatás) 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

2.  A Azure Portal tetején található keresősáv használatával keresse meg és válassza ki **Cloud Services (kiterjesztett támogatás)** lehetőséget.

    :::image type="content" source="media/deploy-portal-1.png" alt-text="A rendszerkép a Azure Portal összes erőforrás paneljét jeleníti meg.":::
 
3.  A Cloud Services (bővített támogatás) panelen válassza a **Létrehozás** lehetőséget. 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="A rendszerkép egy felhőalapú szolgáltatás megvásárlását mutatja be a piactéren.":::

4. A Cloud Services (bővített támogatás) létrehozási ablak megnyílik az **alapok** lapon. 
    - Válasszon ki egy előfizetést.
    - Válasszon egy erőforráscsoportot, vagy hozzon létre egy újat.
    - Adja meg a felhőalapú szolgáltatás (bővített támogatás) központi telepítésének kívánt nevét.
        - A felhőalapú szolgáltatás DNS-neve külön van megadva, és a nyilvános IP-cím DNS-név címkéje adja meg, és a konfiguráció lap nyilvános IP-címe szakaszában módosítható.
    -  Válassza ki a telepítendő régiót.

    :::image type="content" source="media/deploy-portal-3.png" alt-text="A képen a Cloud Services (bővített támogatás) Kezdőlap panel látható.":::

5. Adja hozzá a Cloud Service-konfigurációt, a csomag-és definíciós fájlokat. Hozzáadhat meglévő fájlokat a blob Storage-ból, vagy feltöltheti őket a helyi gépről. Ha a helyi gépről tölt fel feltöltést, ezeket a rendszer egy Storage-fiókban tárolja. 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="A rendszerkép az alapok lap feltöltés szakaszát mutatja a létrehozás során.":::

6. Az összes mező befejezése után lépjen a be elemre, és fejezze be a **konfiguráció** lapot. 
    - Válasszon ki egy virtuális hálózatot a felhőalapú szolgáltatáshoz való hozzárendeléshez, vagy hozzon létre újat. 
        - A Cloud Service (bővített támogatás) központi telepítéseknek virtuális hálózatban **kell lenniük.** A **virtuális hálózatot a** szakasz alatt a szolgáltatás konfigurációs (. cscfg) fájljában is hivatkozni kell `NetworkConfiguration` .
    - Válasszon ki egy meglévő nyilvános IP-címet a Cloud Service-hez való hozzárendeléshez, vagy hozzon létre újat.
        - Ha a szolgáltatás definíciós (. csdef) fájljában definiált **IP-bemeneti végpontok** vannak megadva, akkor a felhőalapú szolgáltatáshoz létre kell hozni egy nyilvános IP-címet. 
        - Cloud Services (kiterjesztett támogatás) csak az alapszintű IP-cím SKU-t támogatja.
        - Ha a szolgáltatás konfigurációja (. cscfg) fenntartott IP-címet tartalmaz, a nyilvános IP-cím lefoglalási típusát a TP **statikus** értékre kell beállítani. 
        - Szükség esetén a Cloud Service-végpont DNS-nevét is hozzárendelheti a Cloud Service-hez társított nyilvános IP-cím DNS-címke tulajdonságának frissítésével.  
    - Választható Indítsa el a Cloud Service szolgáltatást. A létrehozás után azonnal válassza a Start lehetőséget, vagy ne indítsa el a szolgáltatást.
    - Key Vault kiválasztása 
        - Key Vaultra akkor van szükség, ha egy vagy több tanúsítványt ad meg a szolgáltatás konfigurációs (. cscfg) fájljában. Amikor kiválaszt egy kulcstartót, megpróbáljuk megtalálni a kiválasztott tanúsítványokat a szolgáltatás konfigurációs (. cscfg) fájljából a ujjlenyomatai megfelelnek alapján. Ha bármelyik tanúsítvány hiányzik a kulcstartóból, feltöltheti őket, és kattintson a **frissítés** gombra.   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="A rendszerkép a Azure Portal konfiguráció paneljét jeleníti meg Cloud Services (kiterjesztett támogatás) létrehozásakor.":::

7. Az összes mező befejezése után lépjen a **felülvizsgálat és létrehozás** lapra, és ellenőrizze a telepítési konfigurációt, és hozza létre a felhőalapú szolgáltatást (kiterjesztett támogatás).

## <a name="next-steps"></a>Következő lépések 
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).
- Látogasson el a [Cloud Services (bővített támogatás) minták tárházára](https://github.com/Azure-Samples/cloud-services-extended-support)