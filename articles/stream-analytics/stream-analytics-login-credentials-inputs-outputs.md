---
title: Az Azure Stream Analytics-feladatok bejelentkezési hitelesítő adatok forgatása
description: Ez a cikk bemutatja, hogyan frissítse a hitelesítő adatokat a bemeneti és kimeneti fogadók az Azure Stream Analyticsben feladatok.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: aad6f237a37ce4782e2c103a7a14e0a3951093de
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612350"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>A bemenetek és kimenetek egy Stream Analytics-feladat a bejelentkezési hitelesítő adatok forgatása

Minden alkalommal, amikor Ön hozza létre újra a hitelesítő adatokat egy bemeneti vagy a Stream Analytics-feladat kimenetét, frissítenie kell a feladat új hitelesítő adatokkal. A hitelesítő adatok frissítése előtt le kell állítani a feladat, a feladat futása közben nem lehet kicserélni a hitelesítő adatokat. Közötti leállítása és újraindítása a feladat a késés csökkentése érdekében a Stream Analytics az utolsó kimenetét egy feladat folytatása támogatja. Ez a témakör ismerteti a folyamatot a bejelentkezési hitelesítő adatok forgatása és új hitelesítő adatokkal a feladat újraindítása.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Hozza létre újra az új hitelesítő adatokat, és frissítheti a feladatokat az új hitelesítő adatokkal 

Ebben a szakaszban a Microsoft végigvezeti újragenerálása hitelesítő adatokat a Blob Storage, az Event Hubs, az SQL Database és a Table Storage. 

### <a name="blob-storagetable-storage"></a>A BLOB storage és Table storage
1. Jelentkezzen be az Azure Portalon > Tallózás a storage-fiók, amely a Stream Analytics-feladat részeként bemeneti/kimeneti használja.    
2. A beállítások területen nyissa meg a **hozzáférési kulcsok**. A két alapértelmezett kulcsok (1. kulcs, 2. kulcs) között válassza ki azt, amelyik nem használja a feladat, majd ismét:  
   ![Storage-fiók kulcsainak újragenerálása](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Másolja ki az újonnan létrehozott kulcsot.    
4. Az Azure Portalon keresse meg a Stream Analytics-feladat > Válasszon **leállítása** , és várjon, amíg a feladat leállítása.    
5. Keresse meg a Blob/Table storage bemeneti/kimeneti legyen hitelesítő adatainak frissítése.    
6. Keresse meg a **Tárfiókkulcs** mezőbe, majd illessze be az újonnan létrehozott kulcs > kattintson **mentése**.    
7. A kapcsolat tesztelése automatikusan elindul, amikor a módosítások mentése, az értesítések lapon megtekintheti. Nincsenek a két értesítések egy az egyhez felel meg a frissítés mentése, és más felel meg a kapcsolat tesztelése:  
   ![A kulcs módosítása után értesítések](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Lépjen tovább a [indítsa el a feladatot az utolsó leállított időpont](#start-your-job-from-the-last-stopped-time) szakaszban.

### <a name="event-hubs"></a>Event Hubs

1. Jelentkezzen be az Azure Portalon > Tallózás az Eseményközpontba, amely a Stream Analytics-feladat részeként bemeneti/kimeneti használja.    
2. A beállítások területen nyissa meg a **megosztott elérési házirendek** , és válassza ki a hozzáférési házirendet. Között a **elsődleges kulcs** és **másodlagos kulcs**, válassza ki azt, amelyik nem használja a feladatot, majd ismét:  
   ![Az Event Hubs kulcsainak újragenerálása](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Másolja ki az újonnan létrehozott kulcsot.    
4. Az Azure Portalon keresse meg a Stream Analytics-feladat > Válasszon **leállítása** , és várjon, amíg a feladat leállítása.    
5. Keresse meg az Event hubs bemeneti/kimeneti legyen hitelesítő adatainak frissítése.    
6. Keresse meg a **Eseményközpont házirendjének kulcsa** mezőbe, majd illessze be az újonnan létrehozott kulcs > kattintson **mentése**.    
7. A kapcsolat tesztelése automatikusan elindul, amikor menti a módosításokat, győződjön meg arról, hogy azt sikeresen megfelelt.    
8. Lépjen tovább a [indítsa el a feladatot az utolsó leállított időpont](#start-your-job-from-the-last-stopped-time) szakaszban.

### <a name="sql-database"></a>SQL Database

Szeretne csatlakozni az SQL database egy meglévő felhasználó bejelentkezési hitelesítő adatainak frissítéséhez. Hitelesítő adatok az Azure portal vagy egy ügyféloldali eszköz, például az SQL Server Management Studio segítségével frissítheti. Ez a szakasz azt mutatja be a hitelesítő adatok frissítését az Azure portal használatával.

1. Jelentkezzen be az Azure portal > keresse meg az SQL-adatbázis, amely a Stream Analytics-feladat kimeneti részeként használja.    
2. A **adatkezelő**, bejelentkezési és csatlakozás az adatbázishoz > hitelesítési típusként válassza **SQL server-hitelesítés** > írja be a **bejelentkezési** és  **Jelszó** Részletek > Válasszon **Ok**.  
   ![Hozza létre újra az SQL-adatbázishoz tartozó hitelesítő adatokat](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. A lekérdezés lapon módosítható egy, a felhasználó jelszava a következő lekérdezés futtatásával (cserélje le `<user_name>` ezzel a felhasználónévvel és `<new_password>` az új jelszót):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Jegyezze fel az új jelszót.    
5. Az Azure Portalon keresse meg a Stream Analytics-feladat > Válasszon **leállítása** , és várjon, amíg a feladat leállítása.    
6. Keresse meg az SQL database kimeneti, amely a hitelesítő adatok forgatása szeretné. Frissítse a jelszót, és mentse a módosításokat.    
7. A kapcsolat tesztelése automatikusan elindul, amikor menti a módosításokat, győződjön meg arról, hogy azt sikeresen megfelelt.    
8. Lépjen tovább a [indítsa el a feladatot az utolsó leállított időpont](#start-your-job-from-the-last-stopped-time) szakaszban.

### <a name="power-bi"></a>Power BI
1. Jelentkezzen be az Azure portal > keresse meg a Stream Analytics-feladat > Válasszon **leállítása** , és várjon, amíg a feladat leállítása.    
2. Keresse meg a Power BI-kimenet, amelyekhez hitelesítő adatokkal megújításához > kattintson a **engedély megújítása** (megjelenik a sikert jelző üzenet) > **mentése** a módosításokat.    
3. A kapcsolat tesztelése automatikusan elindul, amikor a módosítások mentése, győződjön meg arról, hogy sikeresen megfelelt.    
4. Lépjen tovább a [indítsa el a feladatot az utolsó leállított időpont](#start-your-job-from-the-last-stopped-time) szakaszban.

## <a name="start-your-job-from-the-last-stopped-time"></a>Indítsa el a feladatot az utolsó leállított időpont

1. Keresse meg a feladatot **áttekintése** panelen > Válasszon **Start** elindítani a feladatot.    
2. Válassza ki **utolsó leállítás időpontjában** > kattintson **Start**. Vegye figyelembe, hogy a "Ha az utolsó Leállítás" beállítás csak akkor jelenik meg, ha korábban már futtatta a feladatot, és néhány kimenetet kellett jönnek létre. A feladat az utolsó kimeneti értéket időben újraindításakor alapján.
   ![A Stream Analytics-feladat indítása](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
