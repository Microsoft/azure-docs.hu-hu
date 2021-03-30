---
title: A Azure AD Connect telepítése SQL meghatalmazott rendszergazdai engedélyekkel | Microsoft Docs
description: Ez a témakör olyan Azure AD Connect frissítését ismerteti, amely lehetővé teszi a telepítését olyan fiókkal, amely csak az SQL dbo engedélyekkel rendelkezik.
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f082ec896bf0542b63c8c1d0257679681334050
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85358667"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Az Azure AD Connect telepítése SQL-lel delegált rendszergazdai engedélyekkel
A legújabb Azure AD Connect létrehozása előtt a felügyeleti delegálás a szükséges SQL-konfigurációk telepítésekor nem támogatott.  Azok a felhasználók, akik számára telepíteni szeretnék Azure AD Connect szükségesek ahhoz, hogy a kiszolgáló-rendszergazdai (SA) engedélyekkel rendelkezzenek az SQL Serveren.

A Azure AD Connect legújabb kiadásával az adatbázis üzembe helyezése mostantól az SQL-rendszergazda sávon kívül is elvégezhető, majd a Azure AD Connect rendszergazdája telepítheti az adatbázis-tulajdonosi jogosultságokkal.

## <a name="before-you-begin"></a>Előkészületek
A szolgáltatás használatához fel kell ismernie, hogy több mozgó rész is van, és mindegyikhez tartozhat egy másik rendszergazda a szervezetben.  A következő táblázat összefoglalja az egyes szerepköröket és azok feladatait a Azure AD Connect ezen szolgáltatással való üzembe helyezésével kapcsolatban.

|Szerepkör|Leírás|
|-----|-----|
|Tartományi vagy erdőszintű AD-rendszergazda|Létrehozza a Azure AD Connect által a szinkronizálási szolgáltatás futtatásához használt tartományi szintű szolgáltatásfiókot.  További információ a szolgáltatásfiókok használatáról: [fiókok és engedélyek](reference-connect-accounts-permissions.md).
|SQL-rendszergazda|Létrehozza a ADSync-adatbázist, és hozzáférést biztosít a Azure AD Connect rendszergazdához és a tartomány/erdő rendszergazdája által létrehozott szolgáltatásfiók-hozzáféréshez.|
Azure AD Connect rendszergazda|Telepíti Azure AD Connect és megadja a szolgáltatásfiókot az egyéni telepítés során.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>A Azure AD Connect SQL-delegált engedélyekkel történő telepítésének lépései
Az alábbi lépéseket követve kiépítheti az adatbázist sávon kívül, és telepítheti Azure AD Connect adatbázis-tulajdonosi engedélyekkel.

>[!NOTE]
>Bár ez nem kötelező, **erősen ajánlott** , hogy az adatbázis létrehozásakor a Latin1_General_CI_AS rendezés legyen kiválasztva.


1. Az SQL-rendszergazda hozza létre a ADSync-adatbázist a kis-és nagybetűket megkülönböztető rendezési sorrendtel **(Latin1_General_CI_AS)**.  Az adatbázisnak **AdSync** nevűnek kell lennie.  A helyreállítási modell, a kompatibilitási szint és a tároló típusa a Azure AD Connect telepítésekor a megfelelő értékekre frissül.  A rendezési sorrendet azonban az SQL-rendszergazda helyesen kell beállítani, különben a Azure AD Connect letiltja a telepítést.  Az SA helyreállításához törölnie kell az adatbázist, majd újra létre kell hoznia.
 
   ![Rendezés](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Adja meg a Azure AD Connect rendszergazdájának és a tartományi szolgáltatás fiókjának a következő engedélyeket:
   - SQL-bejelentkezés 
   - **adatbázis-tulajdonosi (dbo)** jogosultságok.
 
   ![Engedélyek](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >A Azure AD Connect nem támogatja a beágyazott tagsággal rendelkező bejelentkezéseket.  Ez azt jelenti, hogy a Azure AD Connect rendszergazdai fiókjának és a tartományi szolgáltatás fiókjának olyan bejelentkezéshez kell tartoznia, amely dbo jogosultságokat kap.  Nem lehet egyszerűen egy olyan csoport tagja, amely dbo jogokkal rendelkező bejelentkezéshez van rendelve.

3. Küldjön e-mailt a Azure AD Connect rendszergazdának, amely a Azure AD Connect telepítésekor használandó SQL Server és példánynév nevét jelzi.

## <a name="additional-information"></a>További információ
Az adatbázis kiépítését követően a Azure AD Connect rendszergazdája kényelmesen telepítheti és konfigurálhatja a helyszíni szinkronizálást.

Abban az esetben, ha az SQL-rendszergazda visszaállította a ADSync-adatbázist egy korábbi Azure AD Connect biztonsági másolatból, telepítenie kell az új Azure AD Connect-kiszolgálót egy meglévő adatbázis használatával. A Azure AD Connect meglévő adatbázissal történő telepítésével kapcsolatos további információkért lásd: [Azure ad Connect telepítése meglévő AdSync-adatbázis használatával](how-to-connect-install-existing-database.md).

## <a name="next-steps"></a>Következő lépések
- [Első lépések az Azure AD Connecttel a gyorsbeállítások használatával](how-to-connect-install-express.md)
- [Az Azure AD Connect testreszabott telepítése](how-to-connect-install-custom.md)
- [Az Azure AD Connect telepítése meglévő ADSync-adatbázis használatával](how-to-connect-install-existing-database.md)  
