---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: 72857564a6b195353e7bf2a27c8369004d52d3f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95971311"
---
<!-- Use the portal to create a media services account. -->

## <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Kattintson **az + erőforrás létrehozása**  >  **adathordozó**-  >  **Media Services** elemre.
1. A **Media Services fiók létrehozása** szakaszban adja meg a szükséges értékeket.

    | Név | Leírás |
    | ---|---|
    |**Fiók neve**|Adja meg az új Media Services fiók nevét. A Media Services-fiók neve csak számokat és kisbetűket tartalmazhat, nem tartalmazhat szóközöket, és 3–24 karakterből állhat.|
    |**Előfizetés**|Ha egynél több előfizetéssel rendelkezik, válasszon egyet azon Azure-előfizetések listájából, amelyekhez hozzáfér.|
    |**Erőforráscsoport**|Válassza ki az új vagy meglévő erőforrást. Az erőforráscsoport közös életciklussal, engedélyekkel és házirendekkel rendelkező erőforrások gyűjteménye. További információ [itt](../../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Hely**|Válassza ki azt a földrajzi régiót, amelyet a rendszer a Media Services-fiókhoz tartozó média és metaadat-rekordok tárolására fog használni. A rendszer e régió alapján fogja feldolgozni, illetve streamelni a médiafájlokat. A legördülő listában csak a Media Services szolgáltatásban elérhető régiók jelennek meg. |
    |**Storage-fiók**|Válassza ki azt a Storage-fiókot, amely a Media Services fiókjából származó médiatartalom blob Storage-tárolóját adja meg. Választhat egy, a Media Services-fiókkal azonos földrajzi régióban elhelyezkedő meglévő tárfiókot, vagy új fiókot is létrehozhat. A újonnan létrehozott tárfiók is ugyanahhoz a régióhoz fog tartozni. A tárfiók nevére ugyanazok a szabályok vonatkoznak, mint a Media Services-fiókok nevére.<br/><br/>Egy **elsődleges** és tetszőleges számú **másodlagos** Storage-fiókot társíthat a Media Services-fiókhoz. A Azure Portal másodlagos Storage-fiókok hozzáadására is használható. További információ: [Azure Storage-fiókok Azure Media Services fiókokkal](../storage-account-concept.md).<br/><br/>A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie. Erősen ajánlott a tárfiókokat és a Media Services-fiókot azonos helyen használni a további késés és kimenő adatforgalomból fakadó költségek elkerülése végett.|
    |**Speciális beállítások**| A rendszer által felügyelt identitás használatával létrehozhat egy fiókot a **rendszer által felügyelt** választógomb kiválasztásával.  Ez a beállítás lehetővé teszi az ügyfelek által felügyelt kulcsok használatát, vagy a saját kulcs (BYOK) és a Media Services használatával engedélyezheti a megbízható tárolást.  Az ügyfél által felügyelt kulcsokkal kapcsolatos további információkért lásd: [saját kulcs használata (ügyfél által felügyelt kulcsok) a Media Services](../concept-use-customer-managed-keys-byok.md). Emellett a [felügyelt identitások](../concept-managed-identities.md) is engedélyezve lesznek.

1. A fióklétrehozás előrehaladásának megtekintéséhez kattintson a **Rögzítés az irányítópulton** elemre.
1. Kattintson az űrlap alján található **Létrehozás** lehetőségre.
