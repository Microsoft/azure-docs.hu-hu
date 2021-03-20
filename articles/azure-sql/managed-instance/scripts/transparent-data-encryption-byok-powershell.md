---
title: 'PowerShell: engedélyezze a saját kulcsú (BYOK) TDE'
titleSuffix: Azure SQL Managed Instance
description: Ismerje meg, hogyan konfigurálhatja az Azure SQL felügyelt példányát úgy, hogy a PowerShell használatával megkezdheti a saját kulcsú (BYOK) transzparens adattitkosítás (TDE) használatát a titkosításhoz.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, sstein
ms.date: 08/25/2020
ms.openlocfilehash: 34a849fde315b45bdb1df577cf26c91f458abd72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323227"
---
# <a name="transparent-data-encryption-in-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>transzparens adattitkosítás az SQL felügyelt példányában a saját kulcsának használatával Azure Key Vault

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Ez a PowerShell-parancsfájl transzparens adattitkosítást (TDE) konfigurál a felügyelt Azure SQL-példányok ügyfél által felügyelt kulcsával, Azure Key Vault-kulcs használatával. Ezt gyakran a TDE-hoz készült saját kulcsú (BYOK) forgatókönyvnek nevezzük. További információ: [Azure SQL transzparens adattitkosítás ügyfél által felügyelt kulccsal](../../database/transparent-data-encryption-byok-overview.md).

## <a name="prerequisites"></a>Előfeltételek

- Egy meglévő felügyelt példány. Lásd: [felügyelt példány létrehozása a PowerShell használatával](create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

A PowerShell helyi vagy Azure Cloud Shell használatával Azure PowerShell 2.3.2 vagy újabb verzió szükséges. Ha frissítenie kell, tekintse meg a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című részt, vagy futtassa az alábbi minta parancsfájlt a modul telepítéséhez az aktuális felhasználóhoz:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-scripts"></a>Mintaparancsfájlok 

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Következő lépések

További információ a Azure PowerShellről: [Azure PowerShell dokumentáció](/powershell/azure/).

Az SQL felügyelt példányaihoz további PowerShell-szkriptek is megtalálhatók az [Azure SQL felügyelt példány PowerShell-parancsfájljaiban](../../database/powershell-script-content-guide.md).
