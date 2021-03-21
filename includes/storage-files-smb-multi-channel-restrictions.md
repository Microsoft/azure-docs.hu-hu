---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 06db7bcb5698f152dd5062762fdb3d59ae326e22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102603277"
---
Az Azure-fájlmegosztás többcsatornás SMB-je jelenleg a következő korlátozásokkal rendelkezik:
- Csak helyileg redundáns FileStorage-fiókokkal használható.
- Csak Windows-ügyfelek esetén támogatott. 
- A csatornák maximális száma négy.
- Az SMB Direct nem támogatott.
- A Storage-fiókokhoz tartozó magánhálózati végpontok nem támogatottak.
- A helyszíni Active Directory Domain Services (AD DS) vagy az Azure AD DS [identitás-alapú hitelesítéssel](../articles/storage/files/storage-files-active-directory-overview.md) rendelkező Storage-fiókok esetében a Azure Files esetében az SMB-ügyfelek nem tudják a Windows fájlkezelővel KONFIGURÁLNI az NTFS-engedélyeket a címtárakban és fájlokban.
    - Az engedélyek konfigurálása helyett használja a Windows [icacls](/windows-server/administration/windows-commands/icacls) eszközt vagy a [set-ACL](/powershell/module/microsoft.powershell.security/set-acl) parancsot.

