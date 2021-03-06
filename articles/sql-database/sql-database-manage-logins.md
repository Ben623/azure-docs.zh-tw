---
title: 登入和使用者
description: 瞭解 Azure SQL Database 和 Azure Synapse 分析如何使用登入和使用者帳戶來驗證使用者的存取權，並使用角色和明確許可權來授權登入和使用者在資料庫內以及伺服器層級上執行動作。
keywords: sql 資料庫安全性, 資料庫安全性管理, 登入安全性, 資料庫安全性, 資料庫存取權
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2020
ms.openlocfilehash: 7c70d5dd19ec0495fe09152b5653363ad369347c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268908"
---
# <a name="granting-database-access-and-authorization-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>使用登入和使用者帳戶將資料庫存取和授權授與 SQL Database 和 Azure Synapse 分析

Azure SQL Database 和 Azure Synapse Analytics （先前稱為 Azure SQL 資料倉儲）中的資料庫驗證存取是使用登入和使用者帳戶進行管理。 [**驗證**](sql-database-security-overview.md#authentication)是證明使用者是誰宣稱的程式。

- 登入是 master 資料庫中的個別帳戶
- 使用者帳戶是任何資料庫中的個別帳戶，不一定要與登入相關聯

> [!IMPORTANT]
> Azure SQL Database 和 Azure Synapse Analytics （先前稱為 Azure SQL 資料倉儲）中的資料庫，在本文的其餘部分會統稱為 Azure SQL Database （以簡化）。

資料庫使用者會使用使用者帳戶連接到 Azure SQL 資料庫，並使用下列兩種方法的其中一種來進行驗證：

- [SQL 驗證](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)，其中包含儲存在 Azure SQL Database 中的登入名稱、使用者帳戶名稱和相關聯的密碼。
- [Azure Active Directory 驗證](sql-database-aad-authentication.md)，其使用儲存在 Azure Active Directory 中的登入認證

在 Azure SQL database 記憶體取資料及執行各種動作的授權，是使用資料庫角色和明確許可權來管理。 「[**授權**](sql-database-security-overview.md#authorization)」是指在 Azure SQL Database 內指派給使用者的許可權，並決定使用者允許執行的動作。 授權是由使用者帳戶的資料庫[角色成員資格](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)和[物件層級許可權](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)所控制。 最好的作法是，您應該授與使用者所需的最低權限。

在本文中，您將了解：

- 一開始建立新 Azure SQL Database 之後的存取和授權設定
- 如何在 master 資料庫和使用者帳戶中新增登入和使用者帳戶，然後授與這些帳戶系統管理許可權
- 如何在使用者資料庫中新增使用者帳戶，其與登入相關聯或作為包含的使用者帳戶
- 使用資料庫角色和明確許可權，在使用者資料庫中設定具有許可權的使用者帳戶

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>建立新資料庫後的現有登入和使用者帳戶

當您建立第一個 Azure SQL Database 部署時，您會為該登入指定管理員登入和相關聯的密碼。 此系統管理帳戶稱為**伺服器管理員**。主要和使用者資料庫中的登入和使用者的下列設定會在部署期間發生：

- 具有系統管理許可權的 SQL 登入，會使用您指定的登入名稱來建立。 [登](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login)入是用來登入 SQL Database 的個別使用者帳戶。
- 此登入會被授與所有資料庫的完整系統管理許可權，做為[伺服器層級的主體](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)。 此登入具有 SQL Database 內的所有可用許可權，因此無法加以限制。 在受控實例中，會將此登入加入至[系統管理員（sysadmin）固定伺服器角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles)（此角色不存在於單一或集區資料庫中）。
- 在每個使用者資料庫中，會為此登入建立名為 `dbo` 的[使用者帳戶](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users)。 [Dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)使用者擁有資料庫中的所有資料庫許可權，而且會對應至 `db_owner` 固定資料庫角色。 本文稍後將討論其他固定資料庫角色。

若要識別 SQL server 的系統管理員帳戶，請開啟 Azure 入口網站，然後流覽至 SQL server 或 SQL Database 的 [**屬性**] 索引標籤。

![SQL Server 系統管理員](media/sql-database-manage-logins/sql-admins.png)

> [!IMPORTANT]
> 系統管理員登入名稱在建立後即無法變更。 若要重設邏輯伺服器管理員的密碼，請移至[Azure 入口網站](https://portal.azure.com)，按一下 [ **SQL server**]，從清單中選取伺服器，然後按一下 [**重設密碼**]。 若要重設受控實例伺服器的密碼，請移至 Azure 入口網站，按一下實例，然後按一下 [**重設密碼**]。 您也可以使用 PowerShell 或 Azure CLI。

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>建立具有系統管理許可權的其他登入和使用者

此時，您的 SQL Database 只會設定為使用單一 SQL 登入和使用者帳戶進行存取。 若要建立具有完整或部分系統管理許可權的其他登入，您有下列選項（視您的部署模式而定）：

- **建立具有完整管理許可權的 Azure Active Directory 系統管理員帳戶**

  啟用 Azure Active Directory 驗證，並建立 Azure AD 的系統管理員登入。 您可以使用完整的系統管理許可權，將一個 Azure Active Directory 帳戶設定為 SQL Database 部署的管理員。 此帳戶可以是個人或安全性群組帳戶。 如果您想要使用 Azure AD 帳戶來連接 SQL Database，則**必須**設定 Azure AD 系統管理員。 如需針對所有 SQL Database 部署類型啟用 Azure AD 驗證的詳細資訊，請參閱下列文章：

  - [使用 Azure Active Directory 驗證搭配 SQL 驗證](sql-database-aad-authentication.md)
  - [使用 SQL 設定及管理 Azure Active Directory 驗證](sql-database-aad-authentication-configure.md)

- **在受控實例部署中，建立具有完整系統管理許可權的 SQL 登入**

  - 在受控實例中建立額外的 SQL Server 登入
  - 使用[ALTER SERVER role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)語句，將登入加入至[系統管理員（sysadmin）固定伺服器角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles)。 此登入將具有完整的系統管理許可權。
  - 或者，使用<a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE login</a>語法來建立[Azure AD 登](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi)入。

- **在單一或集區部署中，建立具有有限系統管理許可權的 SQL 登入**

  - 針對單一或集區資料庫部署或受控實例部署，在 master 資料庫中建立額外的 SQL 登入
  - 在與這個新登入相關聯的 master 資料庫中建立使用者帳戶
  - 使用[ALTER SERVER role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)語句（針對 Azure Synapse 分析，使用[sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)語句），在 `master` 資料庫中，將使用者帳戶新增至 `dbmanager`、`loginmanager` 角色或兩者。

  > [!NOTE]
  > `dbmanager` 和 `loginmanager` 角色與受控實例**部署無關。**

  適用于單一或集區資料庫之這些[特殊 master 資料庫角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-)的成員，可讓使用者擁有建立和管理資料庫，或建立和管理登入的許可權。 在屬於 `dbmanager` 角色成員之使用者所建立的資料庫中，該成員會對應到 `db_owner` 固定資料庫角色，而且可以使用 `dbo` 使用者帳戶來登入和管理該資料庫。 這些角色沒有 master 資料庫以外的明確許可權。

  > [!IMPORTANT]
  > 您無法在單一或集區資料庫中，建立具有完整系統管理許可權的其他 SQL 登入。

## <a name="create-accounts-for-non-administrator-users"></a>為非系統管理員的使用者建立帳戶

您可以使用下列兩種方法的其中一種，為非系統管理使用者建立帳戶：

- **建立登入**

  在 master 資料庫中建立 SQL 登入。 然後，在使用者需要存取的每個資料庫中建立使用者帳戶，並將使用者帳戶與該登入建立關聯。 當使用者必須存取多個資料庫，而且您想要保持密碼同步處理時，這是慣用的方法。 不過，這種方法在與異地複寫搭配使用時具有複雜性，因為必須在主伺服器和次要伺服器上建立登入。 如需詳細資訊，請參閱[設定和管理異地還原或容錯移轉的 Azure SQL Database 安全性](sql-database-geo-replication-security-config.md)。
- **建立使用者帳戶**

  在使用者需要存取權的資料庫（也稱為[包含的使用者](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)）中建立使用者帳戶。

  - 使用單一或集區資料庫時，您一律可以建立這種類型的使用者帳戶。
  - 使用不支援[伺服器主體 Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)的受控實例資料庫，您只能在自主[資料庫](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)中建立這種類型的使用者帳戶。 在支援[Azure AD 伺服器主體](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)的受控實例中，您可以建立使用者帳戶來向受控實例進行驗證，而不需要將資料庫使用者建立為自主資料庫使用者。

  使用此方法時，使用者驗證資訊會儲存在每個資料庫中，並自動複寫到異地複寫的資料庫。 不過，如果同一個帳戶存在於多個資料庫中，而且您使用的是 SQL 驗證，您必須手動將密碼同步。 此外，如果使用者在不同的資料庫中具有不同密碼的帳戶，請記住這些密碼可能會造成問題。

> [!IMPORTANT]
> 若要建立對應至 Azure AD 身分識別的內含使用者，您必須使用 SQL Database 中系統管理員的 Azure AD 帳戶來登入。 在受控實例中，具有 `sysadmin` 許可權的 SQL 登入也可以建立 Azure AD 登入或使用者。

如需示範如何建立登入和使用者的範例，請參閱：

- [建立單一或集區資料庫的登入](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [建立受控實例資料庫的登入](https://docs.microsoft.com/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [建立 Azure Synapse 分析資料庫的登入](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [建立使用者](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [建立 Azure AD 包含的使用者](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> 如需包含在單一或集區資料庫中建立所包含使用者 SQL Server 的安全性教學課程，請參閱[教學課程：保護單一或](sql-database-security-tutorial.md)集區資料庫。

## <a name="using-fixed-and-custom-database-roles"></a>使用固定和自訂資料庫角色

在資料庫中建立使用者帳戶之後，您可以根據登入或以包含的使用者身分，授權該使用者執行各種動作，並存取特定資料庫中的資料。 您可以使用下列方法來授權存取：

- **固定資料庫角色**

  將使用者帳戶加入至[固定資料庫角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)。 有9個固定的資料庫角色，每個都有一組已定義的許可權。 最常見的固定資料庫角色包括： **db_owner**、 **db_ddladmin**、 **db_datawriter**、 **db_datareader**、 **db_denydatawriter**和**db_denydatareader**。 **db_owner** 通常是用來將完整權限授與少數幾個使用者。 其他固定的資料庫角色適用於快速開發簡單的資料庫，但不建議用於大多數實際執行資料庫。 例如， **db_datareader**固定資料庫角色會授與資料庫中每個資料表的讀取存取權，而這不是絕對必要的。

  - 若要將使用者加入至固定資料庫角色：

    - 在 Azure SQL Database 中，請使用[ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)語句。 如需範例，請參閱[ALTER ROLE 範例](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure Synapse 分析，請使用[sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)語句。 如需範例，請參閱[sp_addrolemember 範例](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)。

- **自訂資料庫角色**

  使用[CREATE role](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)語句來建立自訂資料庫角色。 自訂角色可讓您建立自己的使用者定義資料庫角色，並謹慎地授與每個角色在業務需求上所需的最小許可權。 接著，您可以將使用者新增至自訂角色。 當使用者是多個角色的成員時，會集所有這些角色的權限在一身。
- **直接授與許可權**

  直接授與使用者帳戶[許可權](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)。 有超過 100 個權限可在 SQL Database 中分別授與或拒絕。 這些權限有許多為巢狀。 例如，結構描述上的 `UPDATE` 權限包括該結構描述中每個資料表的 `UPDATE` 權限。 如同大多數的權限系統，拒絕權限會覆寫授與權限。 因為權限的巢狀本質和數目，可能需要仔細研究，設計適當的權限系統以便適當地保護您的資料庫。 請從[權限 (Database Engine)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) 的權限清單開始著手，然後檢閱[海報大小的權限圖](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png)。

## <a name="using-groups"></a>使用群組

有效率的存取管理會使用指派給 Active Directory 安全性群組和固定或自訂角色的許可權，而不是個別使用者。

- 使用 Azure Active Directory 驗證時，請將 Azure Active Directory 的使用者放入 Azure Active Directory 安全性群組中。 建立群組的自主資料庫使用者。 將一個或多個資料庫使用者放入自訂資料庫角色，並具有適合該使用者群組的特定許可權。

- 使用 SQL 驗證時，請在資料庫中建立自主資料庫使用者。 將一個或多個資料庫使用者放入自訂資料庫角色，並具有適合該使用者群組的特定許可權。

  > [!NOTE]
  > 您也可以使用非自主資料庫使用者的群組。

您應該熟悉下列功能，這些功能可用來限制或提高權限︰

- [模擬](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server)和[模組簽署](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)可用來安全地暫時提升權限。
- [資料列層級安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) 可用於使用者可存取資料列的限制。
- [資料遮罩](sql-database-dynamic-data-masking-get-started.md) 可用來限制公開機密資料。
- [預存程序](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) 可用來限制對資料庫可採取的動作。

## <a name="next-steps"></a>後續步驟

如需所有 SQL Database 安全性功能的概觀，請參閱 [SQL 安全性概觀](sql-database-security-overview.md)。
