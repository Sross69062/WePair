Jobs_Delete
***************************************************************************************************
```sql
-- =============================================
-- Author:		Stephen Ross
-- Create date: 10-06-2023
-- Description:	A procedure to update status of a job/essentially soft deleting it
-- Code Reviewer:

-- MODIFIED BY:
-- MODIFIED DATE:
-- Code Reviewer:
-- Note:
-- =============================================

CREATE proc [dbo].[Jobs_Delete]
			@Id int

as

/*

	Declare @Id int = 4

	Execute dbo.Jobs_Delete
			@Id

*/

Begin
	
	Declare @DateNow datetime2(7) = GETUTCDATE()

	Update dbo.Jobs
	Set [IsActive] = 0
		,[DateModified] = @DateNow
	Where Id = @Id

End
GO
```
***************************************************************************************************
Jobs_Insert
***************************************************************************************************
```sql
-- =============================================
-- Author:		Stephen Ross
-- Create date: 10-06-2023
-- Description:	A procedure to insert new job information also inserts into jobsskills bridge table.
-- Code Reviewer:

-- MODIFIED BY:
-- MODIFIED DATE:
-- Code Reviewer:
-- Note:
-- =============================================

CREATE proc [dbo].[Jobs_Insert]
			@JobType int
			,@LocationId int
			,@Title nvarchar(200)
			,@Description nvarchar(4000)
			,@Requirements nvarchar(3000)
			,@ContactName nvarchar(100)
			,@ContactPhone nvarchar(20) = NULL
			,@ContactEmail nvarchar(200) = NULL
			,@BatchSkills dbo.BatchUserSkills READONLY
			,@CreatedBy int
			,@Id int OUTPUT
as

/*

	Declare @JobType int = 1
			,@LocationId int = 257
			,@Title nvarchar(200) = 'Community Health Worker'
			,@Description nvarchar(4000) = 'Assist individuals and communities to adopt healthy behaviors.
			Conducts outreach for medical personnel or health organizations to implement programs in the
			community that promote, maintain, and improve individual and community health. 
			Provides information on available resources, social support, and informal counseling, 
			Advocates for individual and community health needs, and provides services in a setting where there is low 
			potential for exposure to blood and other high risk body fluids, communicable diseases and hazardous materials, 
			chemicals and waste. May collect data to help identify community health needs. 
			Implements the care plan under the supervision of appropriate study care team members, 
			and reports findings to care team partners, educators, and other healthcare providers.'
			,@Requirements nvarchar(3000) = 'Completion of Community Health Care Worker certificate 
			program from an accredited program required. Two years post-secondary education and clinical/medical 
			experience preferred. Current, continuous and trusted member of the community. Experience with 
			community programs or health organizations. Demonstrated skills in creative problem-solving. 
			A high level of communication skills (oral and written) necessary for assessment of 
			health concerns and referral for appropriate service delivery. Ability to effectively and 
			professionally interact with health team members and consumers (e.g. members of the community).'
			,@ContactName nvarchar(100) = 'John Smith'
			,@CreatedBy int = 31
			,@Id int
			,@BatchSkills dbo.BatchUserSkills

	Insert into @BatchSkills
				([Id])
	Values (1,2)

	

	Execute dbo.Jobs_Insert
				@JobType
				,@LocationId
				,@Title
				,@Description
				,@Requirements
				,@ContactName
				,@CreatedBy
				,@Id OUTPUT
*/

Begin



	Insert into dbo.Jobs
			([JobTypeId]
			,[LocationId]
			,[CreatedBy]
			,[Title]
			,[Description]
			,[Requirements]
			,[IsActive]
			,[ContactName]
			,[ContactPhone]
			,[ContactEmail])
	Values (@JobType
			,@LocationId
			,@CreatedBy
			,@Title
			,@Description
			,@Requirements
			,1
			,@ContactName
			,@ContactPhone
			,@ContactEmail)

	Set @Id = SCOPE_IDENTITY()

	EXEC dbo.JobsSkills_Insert
				@Id
				,@BatchSkills

End
GO
```
***************************************************************************************************
Jobs_Search_Paginated
***************************************************************************************************
```sql
-- =============================================
-- Author:		Stephen Ross
-- Create date: 10-11-2023
-- Description:	A procedure to bring back paginated information about jobs
-- using a search query. Only returns active jobs.
-- Code Reviewer:

-- MODIFIED BY:
-- MODIFIED DATE:
-- Code Reviewer:
-- Note:
-- =============================================

CREATE proc [dbo].[Jobs_Search_Paginated]
			@PageIndex int
			,@PageSize int
			,@Query nvarchar(50)

as

/*

	Declare @PageIndex int = 0
			,@PageSize int = 6
			,@Query nvarchar(50) = 'health'
			

	Execute dbo.Jobs_Search_Paginated
			@PageIndex
			,@PageSize
			,@Query

*/

Begin

	DECLARE @offset int = @PageIndex * @PageSize
	
	Select j.Id
			,jt.Id
			,jt.Type
			,j.Title
			,j.Description
			,j.Requirements
			,l.Id as locationId
			,lt.Id as locationTypeId
			,lt.Name
			,l.LineOne
			,l.LineTwo
			,l.City
			,l.Zip
			,s.Id
			,s.Name
			,l.Longitude
			,l.Latitude
			,l.DateCreated
			,l.DateModified
			,j.IsActive
			,j.ContactName
			,j.ContactPhone
			,j.ContactEmail
			,CreatedBy = dbo.fn_GetBaseUserJSON(j.CreatedBy)
			,j.DateCreated
			,j.DateModified
			,@PageIndex
			,@PageSize
			,TotalCount = COUNT(1) OVER()
	from dbo.Jobs as j inner join dbo.Locations as l
				on j.LocationId = l.Id
			inner join dbo.States as s
				on l.StateId = s.Id
			inner join dbo.JobTypes as jt
			on j.JobTypeId = jt.Id
			inner join dbo.LocationTypes as lt
				on l.LocationTypeId = lt.Id
	where (j.Title like '%' + @Query + '%' 
	or j.Description like '%' + @Query + '%' 
	or j.Requirements like '%' + @Query + '%' 
	or l.City like '%' + @Query + '%'
	or l.Zip like '%' + @Query + '%'
	or s.Name like '%' + @Query + '%'
	or jt.Type like '%' + @Query + '%') and j.IsActive = 1
	order by j.Id

	OFFSET @offset ROWS
			FETCH NEXT @PageSize ROWS ONLY
End
GO
```
***************************************************************************************************
Jobs_Select_ByCreatedBy
***************************************************************************************************
```sql
-- =============================================
-- Author:		Stephen Ross
-- Create date: 10-06-2023
-- Description:	A procedure to bring back paginated information about jobs base on which user created them
-- Code Reviewer:

-- MODIFIED BY:
-- MODIFIED DATE:
-- Code Reviewer:
-- Note:
-- =============================================

CREATE proc [dbo].[Jobs_Select_ByCreatedBy]
			@PageIndex int
			,@PageSize int
			,@UserId int

as

/*

	Declare @PageIndex int = 0
			,@PageSize int = 3
			,@UserId int = 31

	Execute dbo.Jobs_Select_ByCreatedBy
			@PageIndex
			,@PageSize
			,@UserId

*/

Begin

	DECLARE @offset int = @PageIndex * @PageSize
	
	Select j.Id
			,jt.Id
			,jt.Type
			,j.Title
			,j.Description
			,j.Requirements
			,l.Id as locationId
			,lt.Id as locationTypeId
			,lt.Name
			,l.LineOne
			,l.LineTwo
			,l.City
			,l.Zip
			,s.Id
			,s.Name
			,l.Longitude
			,l.Latitude
			,l.DateCreated
			,l.DateModified
			,j.IsActive
			,j.ContactName
			,j.ContactPhone
			,j.ContactEmail
			,CreatedBy = dbo.fn_GetBaseUserJSON(j.CreatedBy)
			,j.DateCreated
			,j.DateModified
			,@PageIndex
			,@PageSize
			,TotalCount = COUNT(1) OVER()
	from dbo.Jobs as j inner join dbo.Locations as l
				on j.LocationId = l.Id
			inner join dbo.States as s
				on l.StateId = s.Id
			inner join dbo.JobTypes as jt
			on j.JobTypeId = jt.Id
			inner join dbo.LocationTypes as lt
				on l.LocationTypeId = lt.Id
	Where j.CreatedBy = @UserId and j.IsActive = 1
	Order by j.CreatedBy

	OFFSET @offset ROWS
			FETCH NEXT @PageSize ROWS ONLY
End
GO
```
***************************************************************************************************
Jobs_SelectAll
***************************************************************************************************
```sql
-- =============================================
-- Author:		Stephen Ross
-- Create date: 10-11-2023
-- Description:	A procedure to bring back paginated information about all active jobs
-- Code Reviewer:

-- MODIFIED BY:
-- MODIFIED DATE:
-- Code Reviewer:
-- Note:
-- =============================================

CREATE proc [dbo].[Jobs_SelectAll]
			@PageIndex int
			,@PageSize int

as

/*

	Declare @PageIndex int = 0
			,@PageSize int = 10

	Execute dbo.Jobs_SelectAll
			@PageIndex
			,@PageSize

*/

Begin

	DECLARE @offset int = @PageIndex * @PageSize
	
	Select j.Id
			,jt.Id
			,jt.Type
			,j.Title
			,j.Description
			,j.Requirements
			,l.Id as locationId
			,lt.Id as locationTypeId
			,lt.Name
			,l.LineOne
			,l.LineTwo
			,l.City
			,l.Zip
			,s.Id
			,s.Name
			,l.Longitude
			,l.Latitude
			,l.DateCreated
			,l.DateModified
			,j.IsActive
			,j.ContactName
			,j.ContactPhone
			,j.ContactEmail
			,CreatedBy = dbo.fn_GetBaseUserJSON(j.CreatedBy)
			,j.DateCreated
			,j.DateModified
			,@PageIndex
			,@PageSize
			,TotalCount = COUNT(1) OVER()
	from dbo.Jobs as j inner join dbo.Locations as l
				on j.LocationId = l.Id
			inner join dbo.States as s
				on l.StateId = s.Id
			inner join dbo.JobTypes as jt
			on j.JobTypeId = jt.Id
			inner join dbo.LocationTypes as lt
				on l.LocationTypeId = lt.Id
	where j.IsActive = 1
	order by j.Id

	OFFSET @offset ROWS
			FETCH NEXT @PageSize ROWS ONLY
End
GO
```
***************************************************************************************************
Jobs_Update
***************************************************************************************************
```sql
-- =============================================
-- Author:		Stephen Ross
-- Create date: 10-07-2023
-- Description:	A procedure to update job information.
-- Code Reviewer:

-- MODIFIED BY:
-- MODIFIED DATE:
-- Code Reviewer:
-- Note:
-- =============================================

CREATE proc [dbo].[Jobs_Update]
			@JobType int
			,@LocationId int
			,@Title nvarchar(200)
			,@Description nvarchar(4000)
			,@Requirements nvarchar(3000)
			,@ContactName nvarchar(100)
			,@ContactPhone nvarchar(20)
			,@ContactEmail nvarchar(200)
			,@CreatedBy int
			,@Id int
as

/*

	Declare @JobType int = 1
			,LocationId int = 257
			,@Title nvarchar(200) = 'Community Health Worker'
			,@Description nvarchar(4000) = 'Assist individuals and communities to adopt healthy behaviors.
			Conducts outreach for medical personnel or health organizations to implement programs in the
			community that promote, maintain, and improve individual and community health. 
			Provides information on available resources, social support, and informal counseling, 
			Advocates for individual and community health needs, and provides services in a setting where there is low 
			potential for exposure to blood and other high risk body fluids, communicable diseases and hazardous materials, 
			chemicals and waste. May collect data to help identify community health needs. 
			Implements the care plan under the supervision of appropriate study care team members, 
			and reports findings to care team partners, educators, and other healthcare providers.'
			,@Requirements nvarchar(3000) = 'Completion of Community Health Care Worker certificate 
			program from an accredited program required. Two years post-secondary education and clinical/medical 
			experience preferred. Current, continuous and trusted member of the community. Experience with 
			community programs or health organizations. Demonstrated skills in creative problem-solving. 
			A high level of communication skills (oral and written) necessary for assessment of 
			health concerns and referral for appropriate service delivery. Ability to effectively and 
			professionally interact with health team members and consumers (e.g. members of the community).'
			,@ContactName nvarchar(100) = 'John Smith'
			,@CreatedBy int = 31
			,@Id int = 1

	

	Execute dbo.Jobs_Update
				@JobType
				,@LocationId
				,@Title
				,@Description
				,@Requirements
				,@ContactName
				,NULL
				,NULL
				,@CreatedBy
				,@Id
*/

Begin

	Declare @CurrentDate datetime2(7) = GETUTCDATE()



	Update dbo.Jobs
	Set		[JobTypeId] = @JobType
			,[LocationId] = @LocationId
			,[Title] = @Title
			,[Description] = @Description
			,[Requirements] = @Requirements
			,[ContactName] = @ContactName
			,[ContactPhone] = @ContactPhone
			,[ContactEmail] = @ContactEmail
			,[CreatedBy] = @CreatedBy
			,[DateModified] = @CurrentDate
	Where Id = @Id

End
GO
```
***************************************************************************************************
Jobs Table
***************************************************************************************************
```sql
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Jobs](
	[Id] [int] IDENTITY(1,1) NOT NULL,
	[JobTypeId] [int] NOT NULL,
	[LocationId] [int] NOT NULL,
	[CreatedBy] [int] NOT NULL,
	[Title] [nvarchar](200) NOT NULL,
	[Description] [nvarchar](4000) NOT NULL,
	[Requirements] [nvarchar](3000) NOT NULL,
	[IsActive] [bit] NOT NULL,
	[ContactName] [nvarchar](100) NOT NULL,
	[ContactPhone] [nvarchar](20) NULL,
	[ContactEmail] [nvarchar](200) NULL,
	[DateCreated] [datetime2](7) NOT NULL,
	[DateModified] [datetime2](7) NOT NULL,
 CONSTRAINT [PK_Jobs] PRIMARY KEY CLUSTERED 
(
	[Id] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
ALTER TABLE [dbo].[Jobs] ADD  CONSTRAINT [DF_Jobs_DateCreated]  DEFAULT (getutcdate()) FOR [DateCreated]
GO
ALTER TABLE [dbo].[Jobs] ADD  CONSTRAINT [DF_Jobs_DateModified]  DEFAULT (getutcdate()) FOR [DateModified]
GO
ALTER TABLE [dbo].[Jobs]  WITH CHECK ADD  CONSTRAINT [FK_Jobs_JobType] FOREIGN KEY([JobTypeId])
REFERENCES [dbo].[JobTypes] ([Id])
GO
ALTER TABLE [dbo].[Jobs] CHECK CONSTRAINT [FK_Jobs_JobType]
GO
ALTER TABLE [dbo].[Jobs]  WITH CHECK ADD  CONSTRAINT [FK_Jobs_Locations] FOREIGN KEY([LocationId])
REFERENCES [dbo].[Locations] ([Id])
GO
ALTER TABLE [dbo].[Jobs] CHECK CONSTRAINT [FK_Jobs_Locations]
GO
***************************************************************************************************
JobsSkills_Insert
***************************************************************************************************
-- =============================================
-- Author:		Stephen Ross
-- Create date: 10-27-2023
-- Description:	inserts data into the jobskills bridge table
-- Code Reviewer:

-- MODIFIED BY:
-- MODIFIED DATE:
-- Code Reviewer:
-- Note:
-- =============================================

Create   proc [dbo].[JobsSkills_Insert]
					@JobId int
					,@SkillIds dbo.BatchUserSkills READONLY

as

/*

	Declare @JobId int = 10
			,@SkillIds dbo.BatchUserSkills

	Insert into @SkillIds
			([SkillId])
	Values(1),(2)

	EXEC dbo.JobsSkills_Insert
			@JobId
			,@SkillIds

*/

Begin

	Insert into dbo.JobsSkills
				([JobId]
				,[SkillId])
	Select @JobId
			,SkillId
	From @SkillIds

End
GO
```
***************************************************************************************************
JobsSkills_SelectByJobId
***************************************************************************************************
```sql
-- =============================================
-- Author:		Stephen Ross
-- Create date: 10-27-2023
-- Description:	Selects all skill associate to a specific job.
-- Code Reviewer:

-- MODIFIED BY:
-- MODIFIED DATE:
-- Code Reviewer:
-- Note:
-- =============================================

CREATE   proc [dbo].[JobsSkills_SelectByJobId]
					@JobId int

as

/*

	Declare @JobId int = 10

	EXEC [dbo].[JobsSkills_SelectByJobId]
			@JobId

*/

Begin

	Select s.Id
			,s.Name
	From dbo.JobsSkills as js
	inner join dbo.Skills as s
	on js.SkillId = s.Id
	Where JobId = @JobId

End
GO
```
***************************************************************************************************
JobSchema
***************************************************************************************************
```sql
export const jobSchema = Yup.object().shape({
  jobTypeId: Yup.number().min(1).required("Field Required"),
  title: Yup.string()
    .max(200, "Max Characters Reached")
    .required("Field Required"),
  description: Yup.string()
    .max(4000, "Max Characters Reached")
    .required("Field Required"),
  requirements: Yup.string()
    .max(3000, "Max Characters Reached")
    .required("Field Required"),
  contact: Yup.string()
    .max(100, "Max Characters Reached")
    .required("Field Required"),
  phone: Yup.string().max(20, "Max Characters Reached"),
  email: Yup.string()
    .max(200, "Max Characters Reached")
    .email("Must be valid email"),
});
```
***************************************************************************************************
JobsSkills_UserSkills_Match
***************************************************************************************************
```sql
-- =============================================
-- Author:		Stephen Ross
-- Create date: 10-28-2023
-- Description:	Selects all skills associated to 
-- a job and only the matching skills associate to a user.
-- Code Reviewer:

-- MODIFIED BY:
-- MODIFIED DATE:
-- Code Reviewer:
-- Note:
-- =============================================

Create   proc [dbo].[JobsSkills_UserSkills_Match]
					@UserId int
					,@JobId int

as

/*

	Declare @UserId int = 285
			,@JobId int = 10

	EXEC [dbo].[JobsSkills_UserSkills_Match]
			@UserId
			,@JobId

*/

Begin

	Select js.SkillId as JobSkillId
			,us.SkillId as UserSkillId
	From dbo.JobsSkills as js
	left outer join dbo.UserSkills as us
	on js.SkillId = us.SkillId and us.UserId = @UserId
	Where js.JobId = @JobId

End
GO
```
***************************************************************************************************
JobSkills Table
***************************************************************************************************
```sql
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[JobsSkills](
	[JobId] [int] NOT NULL,
	[SkillId] [int] NOT NULL,
 CONSTRAINT [PK_JobsSkills] PRIMARY KEY CLUSTERED 
(
	[JobId] ASC,
	[SkillId] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
```
***************************************************************************************************
JobTypes_SelectAll
***************************************************************************************************
```sql
-- =============================================
-- Author:		Stephen Ross
-- Create date: 10-09-2023
-- Description:	A procedure to select all info from JobType lookup
-- Code Reviewer:

-- MODIFIED BY:
-- MODIFIED DATE:
-- Code Reviewer:
-- Note:
-- =============================================

CREATE proc [dbo].[JobTypes_SelectAll]


as

/*

	Execute dbo.JobTypes_SelectAll

*/

Begin


	Select Id
			,Type

	from dbo.JobTypes

End
GO
```
***************************************************************************************************
JobTypes Table
***************************************************************************************************
```sql
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[JobTypes](
	[Id] [int] IDENTITY(1,1) NOT NULL,
	[Type] [nvarchar](100) NOT NULL,
 CONSTRAINT [PK_JobType] PRIMARY KEY CLUSTERED 
(
	[Id] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
```
***************************************************************************************************
UserSkills_SelectByUserId
***************************************************************************************************
```sql
-- =============================================
-- Author:		Stephen Ross
-- Create date: 10-28-2023
-- Description:	Selects skills attached to a specific user.
-- Code Reviewer:

-- MODIFIED BY:
-- MODIFIED DATE:
-- Code Reviewer:
-- Note:
-- =============================================

Create   proc [dbo].[UserSkills_SelectByUserId]
					@UserId int

as

/*

	Declare @UserId int = 285

	EXEC [dbo].[UserSkills_SelectByUserId]
			@UserId

*/

Begin

	Select s.Id
			,s.Name
	From dbo.UserSkills as us
	inner join dbo.Skills as s
	on us.SkillId = s.Id
	Where us.UserId = @UserId

End
GO
```
***************************************************************************************************
