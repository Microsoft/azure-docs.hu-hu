---
title: Tárolás BareMetalon Oracle számítási feladatokhoz
description: Ismerje meg az BareMetal-infrastruktúra Oracle számítási feladatokhoz nyújtott tárterületét.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 685f4d401d05d897b91ad12dbd79f7d175db05c6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559088"
---
# <a name="storage-on-baremetal-for-oracle-workloads"></a>Tárolás BareMetalon Oracle számítási feladatokhoz

Ebben a cikkben áttekintést nyújtunk az Oracle számítási feladatokhoz elérhető BareMetal-infrastruktúra által kínált tárterületről.

Az Oracle-hez szükséges BareMetal-infrastruktúra NetApp Hálózati fájlrendszer (NFS) tárolót kínál. Az NFS-tárolóhoz nem szükséges Oracle Real Application Clusters - RAC-tanúsítvány. További információ: [Oracle RAC Technologies Matrix for Linux Clusters ( Oracle RAC-technológiák mátrixa Linux-fürtökhöz).](https://www.oracle.com/database/technologies/tech-generic-linux-new.html)

Ez a tárolási ajánlat az OEM-partnerek 3. rétegbeli támogatását tartalmazza, A700-as vagy A800-as tárolóvezérlők használatával.

Az BareMetal Infrastructure storage a következő prémium szintű tárolási képességeket kínálja:

- A dNFS protokollon keresztül kínált adat-/napló-/kvórum-/FSA-kötetek.
- Lemezredundania (*Legfeljebb két lemezhiba elleni védelem).*
- Az adatokat kötetenként legfeljebb 100 TB-ra skálázható fel több kötetre.
- Skálázjon fel horizontálisan több tárolóvezérlőre legfeljebb 12 vezérlőre.
- Nincs lemezszintű felügyelet *(lemezek hozzáadása/eltávolítása),* az Infra automatikusan gondoskodott róla.
- Nincs állásidő a fájl tartalmának különböző kötetekbe való újraterjesztése során.
- A kötetek méretének növelése/zsugorításának képessége.
- SnapCenter-integráció a klónozás és a SnapVault használatával való biztonsági mentéshez.
- Adattitkosítás az adattitkosítással, amely támogatja a FIPS-t (140-2).

## <a name="next-steps"></a>Következő lépések

Ismerje meg az BareMetal-infrastruktúra javítási szempontjait.

> [!div class="nextstepaction"]
> [Az Oracle-hez való BareMetal javítási szempontjai](oracle-baremetal-patching.md)

