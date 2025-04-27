OrgMemberDashData
***************************************************************************************************
```C#
public OrgMemberDashData GetOrgMemberDashData(int userId)
{
    OrgMemberDashData dashData = null;
    string procName = "[dbo].[OrganizationMembers_Select_DashInfo]";
    _data.ExecuteCmd(procName, delegate (SqlParameterCollection col)
    {
        col.AddWithValue("@Id", userId);
    }, delegate (IDataReader reader, short set)

    {
        if (dashData == null)
        {
            dashData = new OrgMemberDashData();
        }
        int startingIdx = 0;
        if (set == 0)
        {
            OrgMemberDashProfile profile = new OrgMemberDashProfile();

            profile.Position = reader.GetSafeString(startingIdx++);
            profile.OrgName = reader.GetSafeString(startingIdx++);
            profile.OrgLogo = reader.GetSafeString(startingIdx++);
            profile.UserInfo = reader.DeserializeObject<BaseUser>(startingIdx++);
            profile.CompanyEmail = reader.GetSafeString(startingIdx++);
            profile.OrgId = reader.GetSafeInt32(startingIdx++);

            dashData.Profile = profile;
        }
        if (set == 1)
        {
            if (dashData.AppointmentInfo == null)
            {
                dashData.AppointmentInfo = new List<UserDashAppointmentInfo>();
            }
            UserDashAppointmentInfo appointmentInfo = new UserDashAppointmentInfo();

            appointmentInfo.OrganizationName = reader.GetSafeString(startingIdx++);
            appointmentInfo.IsConfirmed = reader.GetBoolean(startingIdx++);
            appointmentInfo.AppointmentDate = reader.GetSafeDateTime(startingIdx++);

            dashData.AppointmentInfo.Add(appointmentInfo);
        }
        if (set == 2)
        {
            if (dashData.AssignedCourses == null)
            {
                dashData.AssignedCourses = new List<AssignedCourse>();
            }
            AssignedCourse assignedCourse = new AssignedCourse();

            assignedCourse.CourseName = reader.GetSafeString(startingIdx++);
            assignedCourse.CourseSubject = reader.GetSafeString(startingIdx++);

            dashData.AssignedCourses.Add(assignedCourse);
        }
        if (set == 3)
        {
            if (dashData.CoWorkers == null)
            {
                dashData.CoWorkers = new List<CoWorker>();
            }
            CoWorker coWorker = new CoWorker();

            coWorker.Details = reader.DeserializeObject<BaseUser>(startingIdx++);
            coWorker.Position = reader.GetSafeString(startingIdx++);
            coWorker.CompanyEmail = reader.GetSafeString(startingIdx++);

            dashData.CoWorkers.Add(coWorker);
        }
        if (set == 4)
        {
            if (dashData.PastAppointments == null)
            {
                dashData.PastAppointments = new List<UserDashAppointmentInfo>();
            }
            UserDashAppointmentInfo appointmentInfo = new UserDashAppointmentInfo();

            appointmentInfo.OrganizationName = reader.GetSafeString(startingIdx++);
            appointmentInfo.IsConfirmed = reader.GetBoolean(startingIdx++);
            appointmentInfo.AppointmentDate = reader.GetSafeDateTime(startingIdx++);

            dashData.PastAppointments.Add(appointmentInfo);
        }
    });
    return dashData;
}
```
***************************************************************************************************
GetDashData
***************************************************************************************************
```C#
public ActionResult<ItemResponse<OrgMemberDashData>> GetDashData()
{
    int iCode = 200;
    BaseResponse itemResponse = null;

    try
    {
        int userId = _authenticationService.GetCurrentUserId();
        OrgMemberDashData dashData = _service.GetOrgMemberDashData(userId);
        if (dashData == null)
        {
            iCode = 404;
            itemResponse = new ErrorResponse("Records not found.");
        }
        else
        {
            itemResponse = new ItemResponse<OrgMemberDashData> { Item = dashData };
        }
    }
    catch(Exception ex)
    {
        iCode = 500;
        itemResponse = new ErrorResponse(ex.Message.ToString());
        Logger.LogError(ex.Message);
    }

    return StatusCode(iCode, itemResponse);
}
```
***************************************************************************************************
Org Member Dash Data Models
***************************************************************************************************
```C#
public class AssignedCourse
{
    public string CourseName { get; set; }
    
    public string CourseSubject { get; set; }
}
```
***************************************************************************************************
```C#
public class CoWorker
{
    public BaseUser Details { get; set; }

    public string Position { get; set; }

    public string CompanyEmail { get; set; }
}
```
***************************************************************************************************
```C#
public class OrgMemberDashProfile
{
    public string Position { get; set; }
    
    public string OrgName { get; set; }
    
    public string OrgLogo { get; set; }
    
    public BaseUser UserInfo { get; set; }
    
    public string CompanyEmail { get; set; }
    
    public int OrgId { get; set; }
}
```
***************************************************************************************************
```C#
public class OrgMemberDashData : OrgMemberDashProfile
{
    public List<UserDashAppointmentInfo> AppointmentInfo { get; set; }

    public List<AssignedCourse> AssignedCourses { get; set; }
    
    public List<CoWorker> CoWorkers { get; set; }
    
    public List<UserDashAppointmentInfo> PastAppointments { get; set; }
}
```
***************************************************************************************************
