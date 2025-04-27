AdminDashInfo_Select
***************************************************************************************************
```sql
-- =============================================
-- Author:		Stephen Ross
-- Create date: 11-03-2023
-- Description:	Brings back total users, orgs and job posts for admin dash.
-- Code Reviewer:

-- MODIFIED BY:
-- MODIFIED DATE:
-- Code Reviewer:
-- Note:
-- =============================================

Create proc [dbo].[AdminDashInfo_Select]

as

/*

	EXEC dbo.AdminDashInfo_Select

*/

Begin

	Select 
		TotalUsers = (select count(Id) from dbo.Users)
	Select
		TotalOrgs = (select count(Id) from dbo.Organizations)
	Select
		TotalJobs = (select count(Id) from dbo.Jobs)

End
GO
```
***************************************************************************************************
