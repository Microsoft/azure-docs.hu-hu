---
title: Azure Analysis Services kezelése | Microsoft Docs
description: Ez a cikk a Azure Analysis Services-kiszolgálók adminisztrációs és felügyeleti feladatainak kezeléséhez használt eszközöket ismerteti.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 91ffcac98b2b919a8fc131d235e699aad4fa215d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93078900"
---
# <a name="manage-analysis-services"></a>Az Analysis Services felügyelete
Miután létrehozott egy Analysis Services-kiszolgálót az Azure-ban, lehet, hogy bizonyos adminisztrációs és felügyeleti feladatok elvégzéséhez azonnal el kell végeznie az utat. Például futtasson feldolgozást az adatfrissítésre, szabályozhatja, hogy ki férhet hozzá a modellekhez a kiszolgálón, vagy figyelje a kiszolgáló állapotát. Egyes felügyeleti feladatok csak Azure Portalban hajthatók végre, másokat SQL Server Management Studio (SSMS), és néhány feladatot bármelyikben elvégezhet.

## <a name="azure-portal"></a>Azure Portal
[Azure Portal](https://portal.azure.com/) a kiszolgálók létrehozásához és törléséhez, a kiszolgáló erőforrásainak figyeléséhez, a méret módosításához és a kiszolgálókhoz való hozzáféréssel rendelkezők kezeléséhez.  Ha problémák merülnek fel, támogatási kérelmet is küldhet.

![Képernyőkép, amely megjeleníti a Azure Portal, ahol létrehozhat és törölhet kiszolgálókat, figyelheti a kiszolgáló erőforrásait, módosíthatja a méretet, és kezelheti, hogy ki férhet hozzá a kiszolgálókhoz.](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Az Azure-kiszolgálóhoz való csatlakozás ugyanúgy történik, mint a saját szervezetében lévő kiszolgálói példányokhoz való csatlakozáshoz. A SSMS számos olyan feladatot hajthat végre, mint például az adatok feldolgozása vagy a feldolgozási parancsfájl létrehozása, a Szerepkörök kezelése és a PowerShell használata.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Az SSMS letöltése és telepítése
Győződjön meg arról, hogy a legújabb funkciókat és a legsimább felhasználói élményt használja a Azure Analysis Services-kiszolgálóhoz való kapcsolódáskor. a SSMS legújabb verzióját kell használnia. 

[SQL Server Management Studio letöltése](/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Kapcsolódás a SSMS
 A SSMS használatakor a kiszolgálóhoz való első csatlakozás előtt győződjön meg róla, hogy a felhasználóneve szerepel a Analysis Services rendszergazdák csoportban. További információ: a [kiszolgáló-rendszergazdák és az adatbázis-felhasználók](#server-administrators-and-database-users) a cikk későbbi részében.

1. A kapcsolódás előtt le kell kérnie a kiszolgáló nevét. Másolja a kiszolgáló nevét az **Azure Portal** > kiszolgáló > **Áttekintés** > **Kiszolgálónév** részéből.
   
    ![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. Az SSMS > **Object Explorerben** kattintson a **Csatlakozás** > **Analysis Services** lehetőségre.
3. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen illessze be a kiszolgáló nevét, majd a **hitelesítés** területen válasszon a következő hitelesítési típusok közül:   
    > [!NOTE]
    > A hitelesítés típusa, **Active Directory az MFA-támogatással rendelkező univerzális**, ajánlott.

    > [!NOTE]
    > Ha Microsoft-fiókkal, élő AZONOSÍTÓval, a Yahoo-val, a Gmailben vagy a bejelentkezéssel jelentkezik be, hagyja üresen a jelszó mezőt. A Csatlakozás gombra kattintás után a rendszer jelszót kér.

    **Windows-hitelesítés** a Windows tartomány \ Felhasználónév és a jelszó hitelesítő adatainak használatához.

    **Active Directory a jelszó-hitelesítés** szervezeti fiók használatára. Például egy tartományhoz nem csatlakoztatott számítógépről való csatlakozáskor.

    **Active Directory-Universal, MFA-támogatással** , [nem interaktív vagy többtényezős hitelesítés](../azure-sql/database/authentication-mfa-ssms-overview.md)használatára. 
   
    ![Csatlakozás az SSMS-ben](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Kiszolgáló-rendszergazdák és adatbázis-felhasználók
Azure Analysis Services a felhasználók két típusa van, a kiszolgálói rendszergazdák és az adatbázis-felhasználók. Mindkét típusú felhasználónak a Azure Active Directory kell lennie, és a szervezeti e-mail-címmel vagy egyszerű felhasználónévvel kell megadnia. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Kapcsolódási problémák elhárítása
Ha a SSMS használatával csatlakozik, akkor előfordulhat, hogy törölnie kell a bejelentkezési gyorsítótárat. Nincs a lemezre gyorsítótárazva. A gyorsítótár kiürítéséhez zárjuk be, majd indítsa újra a kapcsolódási folyamatot. 

## <a name="next-steps"></a>Következő lépések
Ha még nem telepített táblázatos modellt az új kiszolgálóra, most már jó ideje van. További információkért lásd [az Azure Analysis Servicesben történő üzembe helyezést](analysis-services-deploy.md) ismertető cikket.

Ha már telepített egy modellt a-kiszolgálóra, az ügyfél vagy a böngésző használatával csatlakozhat hozzá. További információért lásd: [adatok Beolvasása Azure Analysis Services kiszolgálóról](analysis-services-connect.md).