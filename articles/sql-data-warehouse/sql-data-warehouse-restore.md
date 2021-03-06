---
title: Azure SQL Data Warehouse 복원 | Microsoft Docs
description: Azure SQL Data Warehouse를 복원하는 방법을 안내합니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/29/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6eba50fbe7c2a7a40b08e37a96adac66583b8251
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781863"
---
# <a name="restoring-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse 복원 
이 문서에서는 Azure Portal 및 PowerShell에서 다음을 수행하는 방법에 대해 알아봅니다.

- 복원 지점 만들기
- 자동 복원 지점 또는 사용자 정의 복원 지점에서 복원
- 삭제된 데이터베이스에서 복원
- 지역 백업에서 복원
- 사용자 정의 복원 지점에서 데이터 웨어하우스 복사본 만들기

> [!NOTE]
> 8/27을 기준으로 서버 간 복원이 알려진 회귀로 인해 비활성화되었습니다. 이를 가장 먼저 수정하고자 적극적으로 노력하고 있습니다. 불편을 끼쳐드려 죄송합니다. 그동안 서버 간 복원에 [지역 백업](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore#restore-from-an-azure-geographical-region)을 활용할 수 있습니다.  
>

## <a name="before-you-begin"></a>시작하기 전에
**DTU 용량을 확인합니다.** 각 SQL Data Warehouse는 기본 DTU 할당량이 있는 SQL server (예: myserver.database.windows.net)에 의해 호스팅됩니다.  SQL Data Warehouse를 복원하기 전에 SQL 서버에 복원 중인 데이터베이스에 대한 DTU 할당량이 충분히 남아 있는지 확인합니다. 필요한 DTU를 계산하거나 더 많은 DTU를 요청하는 방법을 알아보려면 [DTU 할당량 변경 요청][Request a DTU quota change]을 참조합니다.

## <a name="restore-through-powershell"></a>PowerShell을 통해 복원

## <a name="install-powershell"></a>PowerShell 설치 
SQL Data Warehouse에서 Azure PowerShell을 사용하려면 Azure PowerShell 버전 1.0 이상을 설치해야 합니다.  **Get-Module -ListAvailable -Name AzureRM**을 실행하여 버전을 확인할 수 있습니다.  최신 버전은 [Microsoft 웹 플랫폼 설치 관리자][Microsoft Web Platform Installer]를 통해 설치할 수 있습니다.  최신 버전 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법][How to install and configure Azure PowerShell]을 참조하세요.

## <a name="restore-an-active-or-paused-database-using-powershell"></a>PowerShell을 사용한 활성 또는 일시 중지된 데이터베이스 복원
복원 지점에서 데이터베이스를 복원하려면 [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase] PowerShell cmdlet을 사용합니다.

1. Windows PowerShell을 엽니다.

2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.

3. 복원할 데이터베이스가 포함된 구독을 선택합니다.

4. 데이터베이스의 복원 지점을 나열합니다.

5. RestorePointCreationDate를 사용하여 원하는 복원 지점을 선택합니다.

   > [!NOTE]
   > 복원하는 경우 다른 ServiceObjectiveName(DWU) 또는 다른 지역에 있는 다른 서버를 지정할 수 있습니다.

6. 데이터베이스를 원하는 복원 지점으로 복원합니다.

7. 복원된 데이터베이스가 온라인 상태인지 확인합니다.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> 복원이 완료된 후 [복구 후 데이터베이스 구성][Configure your database after recovery]에 따라 복구된 데이터베이스를 구성할 수 있습니다.
>

## <a name="copy-your-data-warehouse-with-user-defined-restore-points-using-powershell"></a>PowerShell을 사용한 사용자 정의 복원 지점을 통해 데이터 웨어하우스 복사
사용자 정의 복원 지점에서 데이터베이스를 복원하려면 [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase] PowerShell cmdlet을 사용합니다.

1. Windows PowerShell을 엽니다.
2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
3. 복원할 데이터베이스가 포함된 구독을 선택합니다.
4. 즉각적인 데이터베이스 복사본에 대한 복원 지점을 만듭니다.
5. 데이터베이스 이름을 임시 이름으로 바꿉니다.
6. 지정된 RestorePointLabel을 사용하여 가장 최근의 복원 지점을 검색합니다.
7. 데이터베이스의 리소스 ID를 가져와서 복원을 시작합니다.
8. 데이터베이스를 원하는 복원 지점으로 복원합니다.
9. 복원된 데이터베이스가 온라인 상태인지 확인합니다.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$TempDatabaseName = "<YourTemporaryDatabaseName>"
$Label = "<YourRestorePointLabel"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzureRmSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

# Rename the database to a temporary name
Set-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -NewName $TempDatabaseName

# Get the most recent restore point with the specified label
$LabelledRestorePoint = Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName | where {$_.RestorePointLabel -eq $Label} | sort {$_.RestorePointCreationDate} | select -Last 1

# Get the resource id of the database
$ResourceId = (Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName).ResourceId

# Restore the database to its original name from the labelled restore point from the temporary database
$RestoredDatabase = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ResourceId $ResourceId -PointInTime $LabelledRestorePoint.RestorePointCreationDate -TargetDatabaseName $DatabaseName

# Verify the status of restored database
$RestoredDatabase.status

# The original temporary database can be deleted at this point

```

## <a name="restore-a-deleted-database-using-powershell"></a>PowerShell을 사용하여 삭제된 데이터베이스 복원
삭제된 데이터베이스를 복원하려면 [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase] cmdlet을 사용합니다.

1. Windows PowerShell을 엽니다.
2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
3. 복원할 삭제된 데이터베이스가 포함된 구독을 선택합니다.
4. 삭제된 특정 데이터베이스를 가져옵니다.
5. 삭제된 데이터베이스를 복원합니다.
6. 복원된 데이터베이스가 온라인 상태인지 확인합니다.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> 복원이 완료된 후 [복구 후 데이터베이스 구성][Configure your database after recovery]에 따라 복구된 데이터베이스를 구성할 수 있습니다.
>

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>PowerShell을 사용하여 Azure 지역에서 복원
데이터베이스를 복구하려면 [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase] cmdlet을 사용합니다.

> [!NOTE]
> 지역 복원을 Gen2로 수행할 수 있습니다! 이렇게 하려면 Gen2 ServiceObjectiveName(예: DW1000**c**)을 선택적 매개 변수로 지정하세요.
>

1. Windows PowerShell을 엽니다.
2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
3. 복원할 데이터베이스가 포함된 구독을 선택합니다.
4. 복구하려는 데이터베이스를 가져옵니다.
5. 데이터베이스 복구 요청을 만듭니다.
6. 지역에서 복원된 데이터베이스의 상태를 확인합니다.

```Powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> 복원이 완료된 후에 데이터베이스를 구성하려면 [복구 후 데이터베이스 구성][Configure your database after recovery]을 참조하세요.
>

원본 데이터베이스가 TDE를 사용할 수 있는 경우 복구된 데이터베이스도 TDE를 사용할 수 있습니다.

## <a name="restore-through-the-azure-portal"></a>Azure Portal을 통해 복원

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Azure Portal을 사용하여 사용자 정의 복원 지점 만들기
1. [Azure Portal][Azure portal]에 로그인합니다.

2. 복원 지점을 만들려는 SQL 데이터 웨어하우스로 이동합니다.

3. [개요] 블레이드의 위쪽에서 **+ 새 복원 지점**을 선택합니다.

    ![새 복원 지점](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. 복원 지점에 대한 이름을 지정합니다.

    ![복원 지점에 대한 이름](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Azure Portal을 사용하여 활성 또는 일시 중지된 데이터베이스 복원
1. [Azure Portal][Azure portal]에 로그인합니다.
2. 복원하려는 SQL 데이터 웨어하우스로 이동합니다.
3. [개요] 블레이드의 위쪽에서 **복원**을 선택합니다.

    ![ 복원 개요](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. **자동 복원 지점** 또는 **사용자 정의 복원 지점** 중 하나를 선택합니다.

    ![자동 복원 지점](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. 사용자 정의 복원 지점의 경우 **복원 지점을 선택**하거나 **새 사용자 정의 복원 지점을 만듭니다**.

    ![사용자 정의 복원 지점](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Azure Portal을 사용하여 삭제된 데이터베이스 복원
1. [Azure Portal][Azure portal]에 로그인합니다.
2. 삭제된 데이터베이스가 호스팅된 SQL 서버로 이동합니다.
3. 목차에서 [삭제된 데이터베이스] 아이콘을 선택합니다.

    ![삭제된 데이터베이스](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_0.png)

4. 복원할 삭제된 데이터베이스를 선택합니다.

    ![삭제된 데이터베이스 선택](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_1.png)

5. 새 데이터베이스 이름을 지정합니다.

    ![데이터베이스 이름 지정](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_2.png)

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
