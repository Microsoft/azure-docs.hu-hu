---
title: CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0 Blueprint minta
description: A CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0 tervrajzi mintájának áttekintése. Ennek a tervmintának a segítségével az ügyfelek adott vezérlőket mérhetnek fel.
ms.date: 03/11/2021
ms.topic: sample
ms.openlocfilehash: 7030b4e9e9ddd3d5bbf1a5a7deaf1e1aa241b38e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565996"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v130-blueprint-sample"></a>CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0 Blueprint minta

A CIS Microsoft Azure Founds benchmark v 1.3.0 Blueprint minta az irányítási őr-síneket olyan [Azure Policy](../../policy/overview.md) segítségével biztosítja, amelyek segítenek az egyes CIS-Microsoft Azure alapítványok teljesítményteszt v 1.3.0 ajánlásainak értékelésében. Ez a terv segít az ügyfeleknek az olyan Azure-beli üzembe helyezett architektúrák alapkészletének üzembe helyezésében, amelyeknek meg kell valósítaniuk a CIS Microsoft Azure Founds teljesítményteszt v 1.3.0-javaslatait.

## <a name="recommendation-mapping"></a>Az ajánlások leképezése

A [Azure Policy ajánlás-hozzárendelés](../../policy/samples/cis-azure-1-3-0.md) részletesen ismerteti a tervben szereplő szabályzat-definíciókat, valamint azt, hogy ezek a szabályzat-definíciók hogyan képezhetők le a CIS **-ben Microsoft Azure** founds teljesítményteszt v 1.3.0. Architektúrához való hozzárendeléskor a rendszer kiértékeli az erőforrásokat a hozzárendelt szabályzat-definíciókkal való meg nem felelés esetén Azure Policy. További információ: [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Üzembe helyezés

Az Azure-tervrajzok (Microsoft Azure CIS) a következő lépéseket kell végrehajtania:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **Közzétettként**
> - A terv másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free), mielőtt hozzákezd.

### <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először a tervminta implementálásához hozzon létre egy új tervet a környezetében, és kiindulópontként használja a mintát.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A bal oldalon válassza az **Első lépések** oldalt, és válassza a _Terv létrehozása_ területen a **Létrehozás** lehetőséget.

1. Keresse meg a **CIS Microsoft Azure founds benchmark v 1.3.0** Blueprint mintát _más minták_ alatt, és válassza **a minta használata** lehetőséget.

1. Adja meg a tervminta _alapvető beállításait_:

   - **Terv neve**: adjon meg egy nevet a CIS Microsoft Azure alapítványok teljesítményteszt-tervezetének mintájának.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az oldal tetején lévő _Összetevők_ lapot, vagy az oldal alján lévő **Következő: Összetevők** lehetőséget.

1. Tekintse át a terv minta részét képező összetevők listáját. Számos összetevőnek olyan paraméterei vannak, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor végzett a tervminta áttekintésével.

### <a name="publish-the-sample-copy"></a>Ugyanazon másolat közzététele

A tervminta másolata létrejött a környezetében. **Piszkozat** módban jött létre, és **közzé kell tenni**, mielőtt hozzárendelhetné és üzembe helyezhetné. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás a CIS Microsoft Azure Founds benchmark v 1.3.0 ajánlásain kívül is áthelyezhető.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki az oldal tetején található **Terv közzététele** lehetőséget. A jobb oldalt megjelenő új oldalon adja meg a tervminta másolatának **verzióját**. Ez a tulajdonság akkor hasznos, ha később módosításokat végez. Adjon meg olyan **módosítási megjegyzéseket** , mint például az "első verzió a CIS Microsoft Azure az alapítók teljesítményteszt-tervezet mintája." Ezután válassza a lap alján található **Közzététel** lehetőséget.

### <a name="assign-the-sample-copy"></a>Ugyanazon másolat hozzárendelése

Miután a tervezet mintájának **közzététele** sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ebben a lépésben adja meg a paramétereket, hogy a tervminta másolatának minden üzemelő példánya egyedi legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki a Tervdefiníció oldal tetején található **Terv hozzárendelése** lehetőséget.

1. Adja meg a tervhozzárendelés paraméterértékeit:

   - Alapvető beállítások

     - **Előfizetések**: válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, és a terv mintájának másolatát mentette. Ha egynél több előfizetést választ ki, mindegyikhez létrejön egy hozzárendelés a beírt paraméterekkel.
     - **Hozzárendelés neve**: a név előre ki van töltve a terv neve alapján.
       Módosítsa igény szerint, vagy hagyja meg az eredetit.
     - **Hely**: válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További információ: [felügyelt identitások az Azure-erőforrásokhoz](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: válasszon egy **közzétett** verziót a terv mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezetének megfelelő tervzárolási beállítást. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás beállítást.

   - Összetevő paraméterei

     Az ebben a szakaszban megadott paraméterek arra az összetevőre érvényesek, amelyben meg lettek határozva. Ezek a paraméterek [dinamikus paraméterek](../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza az oldal alján lévő **Hozzárendelés** lehetőséget. Létrejön a terv hozzárendelése, és megkezdődik az összetevő üzembe helyezése. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának ellenőrzéséhez nyissa meg a terv hozzárendelését.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervminták **ingyenesek**. Az Azure-erőforrások [díjszabása termékalapú](https://azure.microsoft.com/pricing/). A [díjkalkulátorral](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a tervminta által üzembe helyezett erőforrások futtatásának költségét.

### <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a terv összetevő-paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A használatra jóváhagyott virtuálisgép-bővítmények listája|A virtuálisgép-bővítmények pontosvesszővel tagolt listája; a bővítmények teljes listáját a Azure PowerShell parancs használatával tekintheti meg Get-AzVMExtensionImage|
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az SQL felügyelt példányainak az ügyfelek által felügyelt kulcsokat kell használniuk a REST-adatok titkosításához|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az SQL Server sebezhetőségi felmérési beállításaiban szerepelnie kell egy e-mail-címnek a vizsgálati jelentések fogadásához.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a Azure Data Lake Store diagnosztikai naplóit engedélyezni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a lemezes titkosítást a virtuális gépeken kell alkalmazni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a Key vaultnak engedélyezve kell lennie a kiürítési védelemmel|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: gondoskodjon arról, hogy az API-alkalmazás az "on" értékre legyen állítva az "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)" beállítás|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az SQL-kiszolgálóknak az ügyfél által felügyelt kulcsokat kell használniuk a REST-adatok titkosításához|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: felügyelt identitást kell használni a függvényalkalmazás|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az Azure Defender for Key Vault engedélyezése szükséges|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: az egyéni előfizetés tulajdonosi szerepkörei nem létezhetnek|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: a kulcsoknak a lejárati dátumokat kell beállítani|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az SQL-adatbázisokon engedélyezni kell a transzparens adattitkosítás|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a sebezhetőségi felmérést engedélyezni kell a felügyelt SQL-példányon.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha az API-alkalmazás részeként van használatban|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az Azure Defender for App Service engedélyezése szükséges|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a Storage-fiókoknak a virtuális hálózati szabályok használatával kell korlátoznia a hálózati hozzáférést|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: a felügyelt identitást a webalkalmazásban kell használni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az internetről érkező SSH-hozzáférést le kell tiltani|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a nem csatolt lemezeket titkosítani kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: engedélyezni kell az Azure Defender for Storage szolgáltatást|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a Storage-fiókoknak korlátoznia kell a hálózati hozzáférést|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a Logic Apps diagnosztikai naplóit engedélyezni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a IoT Hub diagnosztikai naplóit engedélyezni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a FTPS csak a függvényalkalmazás kell megadni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az adott biztonsági műveletekhez (Microsoft. Security/securitySolutions/delete) tartozó műveletnapló-riasztásnak léteznie kell.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az adott biztonsági műveletekhez (Microsoft. Security/securitySolutions/Write) tartozó műveletnapló-riasztásnak léteznie kell.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: engedélyezze a biztonságos átvitelt a Storage-fiókoknak.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a Batch-fiókokban lévő diagnosztikai naplókat engedélyezni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: a Log Analytics ügynök automatikus kiépítés engedélyezése az előfizetésen|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a Java-verzió a legújabb, ha a webalkalmazás részeként van használatban|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a FTPS kötelező megadni a webalkalmazásban|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az Azure Defender for Servers szolgáltatást engedélyezni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: az előfizetéseknek a biztonsági problémákhoz kapcsolattartó e-mail címmel kell rendelkezniük|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a Storage-fiók nyilvános hozzáférését nem szabad engedélyezni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: engedélyezni kell az Azure Defender for Kubernetes|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: engedélyezni kell a kapcsolatok szabályozását a PostgreSQL adatbázis-kiszolgálóin|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: Győződjön meg arról, hogy a webalkalmazás rendelkezik a "be" értékre beállított "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)" beállítással.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: engedélyezni kell az Azure Defender for SQL-kiszolgálókat a számítógépeken.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: engedélyezni kell a nagy súlyosságú riasztásokra vonatkozó e-mailes értesítést.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: a Storage-fióknak ügyfél által felügyelt kulcsot kell használnia a titkosításhoz|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a webalkalmazás részeként van használatban|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a Function alkalmazás részeként van használatban|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a webalkalmazás részeként van használatban|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha az API-alkalmazás részeként van használatban|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a Virtual Machine Scale Sets diagnosztikai naplóit engedélyezni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az Azure Defender Azure SQL Database-kiszolgálókat engedélyezni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az Event hub diagnosztikai naplóinak engedélyezve kell lennie|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a rendszerfrissítéseket telepíteni kell a gépekre.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a Java-verzió a legújabb, ha az API-alkalmazás részeként van használatban|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: az SQL-kiszolgálókat 90 napos naplózási vagy ennél nagyobb adatmegőrzéssel kell konfigurálni.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a webalkalmazás futtatására használatos|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a legújabb TLS-verziót kell használni az API-alkalmazásban|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az MFA-nak engedélyezve kell lennie az előfizetéséhez írási engedéllyel rendelkező fiókoknak|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a hitelesítést engedélyezni kell a webalkalmazásban.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: a titkoknak lejárati dátumokat kell beállítani|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha az API-alkalmazás futtatásához használatos.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a FTPS csak az API-alkalmazásban kell megadni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: Győződjön meg arról, hogy a Java-verzió a legújabb, ha a Function alkalmazás részeként van használatban|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a webalkalmazásnak csak HTTPS-kapcsolaton keresztül kell elérhetőnek lennie|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az SQL Server naplózását engedélyezni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a speciális adatbiztonságot engedélyezni kell a felügyelt SQL-példányon.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: Role-Based Access Control (RBAC) a Kubernetes-szolgáltatásokban kell használni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: hiányzó Endpoint Protection figyelése Azure Security Center|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a keresési szolgáltatásokban engedélyezni kell a diagnosztikai naplókat.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a App Services diagnosztikai naplóit engedélyezni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az adott felügyeleti műveletekhez (Microsoft. Network/networkSecurityGroups/delete) tartozó műveletnapló-riasztásnak léteznie kell.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: az adott felügyeleti műveletekhez (Microsoft. Network/networkSecurityGroups/securityRules/delete) tartozó műveletnapló-riasztásnak léteznie kell.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az adott felügyeleti műveletekhez (Microsoft. Network/networkSecurityGroups/securityRules/Write) tartozó műveletnapló-riasztásnak léteznie kell.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az adott felügyeleti műveletekhez (Microsoft. Network/networkSecurityGroups/Write) tartozó műveletnapló-riasztásnak léteznie kell.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: az adott felügyeleti műveletekhez (Microsoft. SQL/Servers/firewallRules/delete) tartozó műveletnapló-riasztásnak léteznie kell.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: az adott felügyeleti műveletekhez (Microsoft. SQL/Servers/firewallRules/Write) tartozó műveletnapló-riasztásnak léteznie kell.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: csak a jóváhagyott virtuálisgép-bővítményeket kell telepíteni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: engedélyezni kell az Azure Defender for Container kibocsátásiegység-forgalmi jegyzékeit|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: a felügyelt identitást az API-alkalmazásban kell használni|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a hitelesítésnek engedélyezve kell lennie az API-alkalmazásban|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: az adott házirend-műveletekhez (Microsoft. Authorization/policyAssignments/delete) tartozó műveletnapló-riasztásnak léteznie kell.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: az adott házirend-műveletekhez (Microsoft. Authorization/policyAssignments/Write) tartozó műveletnapló-riasztásnak léteznie kell.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a hitelesítést engedélyezni kell a Function alkalmazásban.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a Data Lake Analytics diagnosztikai naplóit engedélyezni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: a Storage-fiókoknak engedélyezniük kell a megbízható Microsoft-szolgáltatások elérését|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a Key Vault diagnosztikai naplóit engedélyezni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: engedélyezze az SSL-kapcsolat kikényszerített beállítását a PostgreSQL adatbázis-kiszolgálók esetében|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a Function app futtatásához használatos.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: az internetről származó RDP-hozzáférés le kell tiltani|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: engedélyezze az SSL-kapcsolat kikényszerített beállítását a MySQL adatbázis-kiszolgálókon|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: gondoskodjon arról, hogy az alkalmazás "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)" beállítását "be" értékre állítsa be.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a naplózási ellenőrzőpontokat engedélyezni kell a PostgreSQL-adatbázis-kiszolgálókhoz|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a naplózási kapcsolatokat engedélyezni kell a PostgreSQL-adatbázis-kiszolgálókhoz|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a leválasztásokat a PostgreSQL adatbázis-kiszolgálóihoz kell naplózni.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a legújabb TLS-verziót kell használni a webalkalmazásban|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a Service Bus diagnosztikai naplóit engedélyezni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a Azure Stream Analytics diagnosztikai naplóit engedélyezni kell|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Házirend hatása: a legújabb TLS-verziót kell használni a függvényalkalmazás|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A házirend hatása: a BYOK tartalmazó tárolót tartalmazó Storage-fióknak titkosítva kell lennie.|A hatásokkal kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|AK-fürtök belefoglalása, ha a virtuális gépek méretezési csoportjának diagnosztikai naplói engedélyezve vannak||
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A App Services legújabb Java-verziója|A legújabb támogatott Java-verzió a App Services|
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A Linux rendszerhez készült legújabb Python-verzió App Services|A App Services legújabb támogatott Python-verziója|
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Azon régiók listája, amelyeken engedélyezni kell a Network Watcher|A régiók teljes listájának megtekintéséhez futtassa a PowerShell-parancsot Get-AzLocation|
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A App Services legújabb PHP-verziója|A App Services legújabb támogatott PHP-verziója|
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Erőforrás-naplókhoz szükséges megőrzési idő (nap)|Az erőforrás-naplókkal kapcsolatos további információkért tekintse meg a következőt: [https://aka.ms/resourcelogs](../../../azure-monitor/essentials/resource-logs.md) |
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|A Network Watcher erőforráscsoport neve|Azon erőforráscsoport neve, ahol a hálózati figyelők találhatók|
|CIS Microsoft Azure alapítványok teljesítményteszt v 1.3.0|Szabályzat-hozzárendelés|Az SQL-kiszolgálók szükséges naplózási beállítása||

## <a name="next-steps"></a>Következő lépések

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.