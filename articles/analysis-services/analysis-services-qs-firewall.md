---
title: Rövid útmutató – Azure-beli Analysis Services-kiszolgáló tűzfalának konfigurálása | Microsoft Docs
description: Útmutatás tűzfal konfigurálásához egy Azure-beli Analysis Services-kiszolgálópéldányhoz.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0351ab3213ac75559cc025f2ef71e2f098a7504c
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993198"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Rövid útmutató: Kiszolgálótűzfal konfigurálása – Portál

Ez a rövid útmutató segítséget nyújt az Azure Analysis Services-kiszolgáló tűzfalának konfigurálásában. A kiszolgáló és az adatok védelmének fontos része egy tűzfal engedélyezése és IP-címtartományok konfigurálása csak a kiszolgálóhoz hozzáférő számítógépek számára.

## <a name="prerequisites"></a>Előfeltételek

- Analysis Services-kiszolgáló az előfizetésben. További tudnivalókért lásd: [Rövid útmutató: Kiszolgáló létrehozása – Portál](analysis-services-create-server.md) vagy [Rövid útmutató: Kiszolgáló létrehozása – PowerShell](analysis-services-create-powershell.md)
- Egy vagy több IP-címtartomány ügyfélszámítógépekhez (ha szükséges).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra 

[Jelentkezzen be a portálra](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Tűzfal konfigurálása

1. Kattintson a kiszolgálóra az Áttekintés oldal megnyitásához. 
2. A **BEÁLLÍTÁSOK** > **Tűzfal** > **Tűzfal engedélyezése** részben kattintson a **Be** lehetőségre.
3. A DirectQuery-hozzáférés a Power BI szolgáltatásból történő engedélyezéséhez a **Hozzáférés engedélyezése Power BI-ból** területen kattintson a **Be** lehetőségre.  
4. (Opcionális) Adjon meg egy vagy több IP-címtartományt. Adjon meg egy nevet, valamint egy kezdő és egy záró IP-címet mindegyik tartomány számára. 
5. Kattintson a **Save** (Mentés) gombra.

     ![Tűzfalbeállítások](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az IP-címtartományokat, vagy tiltsa le a tűzfalat.

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban megismerte, hogyan konfigurálhat tűzfalat a kiszolgálójához. Most, hogy tűfalas védelmet biztosított a kiszolgáló számára, hozzáadhat egy alapszintű minta adatmodellt a portálról. A mintamodell azért hasznos, mert segít megismerni a modell adatbázis-szerepkörök konfigurálását és az ügyfélkapcsolatok tesztelését. További tudnivalókért folytassa a mintamodell hozzáadását ismertető oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: Mintamodell hozzáadása a kiszolgálóhoz](analysis-services-create-sample-model.md)
