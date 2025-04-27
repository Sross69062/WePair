OrganizationMembers_Select_DashInfo
***************************************************************************************************
-- =============================================
-- Author:		Stephen Ross
-- Create date: 1-17-2024
-- Description: Select info from multiple tables for org member dashboard
-- Code Reviewer:

-- MODIFIED BY:
-- MODIFIED DATE:
-- Code Reviewer:
-- Note:
-- =============================================

CREATE Proc [dbo].[OrganizationMembers_Select_DashInfo]
			@Id int

as

/*

	Declare @Id int = 284

	Execute dbo.OrganizationMembers_Select_DashInfo
				@Id

*/

Begin

	Declare @DateNow datetime2(7) = GETUTCDATE()
			,@OrgId int = (Select OrganizationId
							From dbo.OrganizationMembers
							Where UserId = @Id)

	Select pt.Name
			,o.Name
			,o.Logo
			,UserDetails = dbo.fn_GetBaseUserJSON(@Id)
			,om.UserOrgEmail
			,om.OrganizationId
	From dbo.OrganizationMembers as om
	inner join dbo.OrgPositionTypes as pt
	on om.PositionType = pt.Id
	inner join dbo.Organizations as o
	on om.OrganizationId = o.Id
	Where UserId = @Id

	Select Top 5 AptWith =(Select o.name
							From dbo.Organizations as o
							Where o.CreatedBy = a.ModifiedBy and a.ClientId = @Id)
				,a.IsConfirmed
				,a.AppointmentStart
	From dbo.Appointments as a
	Where ClientId = @Id and a.AppointmentStart >= @DateNow
	order by a.AppointmentStart
	

	Select Top 5 c.Title
				,c.Subject
	From dbo.CoursesAssigned as ca
	inner join dbo.Course as c
	on ca.CourseId = c.Id
	Where ca.UserId = @Id

	Select CoWorkers = dbo.fn_GetBaseUserJSON(om.UserId)
						,pt.Name
						,om.UserOrgEmail
						From dbo.OrganizationMembers as om
						inner join dbo.OrgPositionTypes as pt
						on om.PositionType = pt.Id
						Where OrganizationId = @OrgId and UserId != @Id

	Select Top 5 AptWith =(Select o.name
							From dbo.Organizations as o
							Where o.CreatedBy = a.ModifiedBy and a.ClientId = @Id)
				,a.IsConfirmed
				,a.AppointmentStart
	From dbo.Appointments as a
	Where ClientId = @Id and a.AppointmentStart < @DateNow
	order by a.AppointmentStart desc

End
GO
***************************************************************************************************
OrganizationMembers_Select_ByUserId
***************************************************************************************************
-- =============================================
-- Author:		Stephen Ross
-- Create date: 10-13-2023
-- Description: A proc to select the orginizationId associated to a specific user
-- Code Reviewer:

-- MODIFIED BY:
-- MODIFIED DATE:
-- Code Reviewer:
-- Note:
-- =============================================

CREATE proc [dbo].[OrganizationMembers_Select_ByUserId]
				@UserId int

as

/*

	Declare @UserId int = 8

	Execute dbo.OrganizationMembers_Select_ByUserId
				@UserId

*/

Begin

	Select OrganizationId
	From dbo.OrganizationMembers
	Where UserId = @UserId

End
GO
***************************************************************************************************