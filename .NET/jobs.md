JobService
***************************************************************************************************

public class JobService : IJobService
{
    IDataProvider _data = null;
    ILocationService _locationService = null;
    ILookUpService _lookUpService = null;
    public JobService(IDataProvider data, ILookUpService lookUpService, ILocationService locationService)
    {
        _data = data;
        _lookUpService = lookUpService;
        _locationService = locationService;
    }
    public Paged<Job> SelectAllPaginated(int pageIndex, int pageSize)
    {
        Paged<Job> paged = null;
        List<Job> list = null;
        int totalCount = 0;
        string procName = "[dbo].[Jobs_SelectAll]";

        _data.ExecuteCmd(procName, delegate (SqlParameterCollection paramCollection)
        {
            paramCollection.AddWithValue("@PageIndex", pageIndex);
            paramCollection.AddWithValue("@PageSize", pageSize);
        },
        delegate (IDataReader reader, short set)
        {
            Job job = MapSingleJob(reader);
            if (totalCount == 0)
            {
                totalCount = reader.GetSafeInt32(reader.FieldCount - 1);
            }

            if (list == null)
            {
                list = new List<Job>();
            }
            list.Add(job);
        });

        if (list != null)
        {
            paged = new Paged<Job>(list, pageIndex, pageSize, totalCount);
        }
        return paged;
    }
    public Paged<Job> SearchPaginated(int pageIndex, int pageSize, string query)
    {
        Paged<Job> paged = null;
        List<Job> list = null;
        int totalCount = 0;
        string procName = "[dbo].[Jobs_Search_Paginated]";

        _data.ExecuteCmd(procName, delegate (SqlParameterCollection paramCollection)
        {
            paramCollection.AddWithValue("@PageIndex", pageIndex);
            paramCollection.AddWithValue("@PageSize", pageSize);
            paramCollection.AddWithValue("@Query", query);
        },
        delegate (IDataReader reader, short set)
        {
            Job job = MapSingleJob(reader);
            if (totalCount == 0)
            {
                totalCount = reader.GetSafeInt32(reader.FieldCount - 1);
            }

            if (list == null)
            {
                list = new List<Job>();
            }
            list.Add(job);
        });

        if (list != null)
        {
            paged = new Paged<Job>(list, pageIndex, pageSize, totalCount);
        }
        return paged;
    }
    public Paged<Job> GetByCreateByPaginated(int pageIndex, int pageSize, int userId)
    {
        Paged<Job> paged = null;
        List<Job> list = null;
        int totalCount = 0;
        string procName = "[dbo].[Jobs_Select_ByCreatedBy]";

        _data.ExecuteCmd(procName, delegate (SqlParameterCollection paramCollection)
        {
            paramCollection.AddWithValue("@PageIndex", pageIndex);
            paramCollection.AddWithValue("@PageSize", pageSize);
            paramCollection.AddWithValue("@UserId", userId);
        },
        delegate (IDataReader reader, short set)
        {
            Job job = MapSingleJob(reader);
            if (totalCount == 0)
            {
                totalCount = reader.GetSafeInt32(reader.FieldCount - 1);
            }

            if (list == null)
            {
                list = new List<Job>();
            }
            list.Add(job);
        });

        if (list != null)
        {
            paged = new Paged<Job>(list, pageIndex, pageSize, totalCount);
        }
        return paged;
    }
    public int Add(JobAddRequest model, int userId)
    {
        int id = 0;
        string procName = "[dbo].[Jobs_Insert]";

        _data.ExecuteNonQuery(procName, inputParamMapper: delegate (SqlParameterCollection paramCollection)
        {
            AddCommonParams(model, paramCollection, userId);
            SqlParameter idOut = new SqlParameter("@Id", SqlDbType.Int);
            idOut.Direction = ParameterDirection.Output;

            paramCollection.Add(idOut);
        },
        returnParameters: delegate (SqlParameterCollection returnCollection)
        {
            object oId = returnCollection["@Id"].Value;
            int.TryParse(oId.ToString(), out id);
        });
        return id;
    }
    public void Update(JobUpdateRequest model, int userId)
    {
        string procName = "[dbo].[Jobs_Update]";

        _data.ExecuteNonQuery(procName, inputParamMapper: delegate (SqlParameterCollection paramCollection)
        {
            AddCommonParams(model, paramCollection, userId);
            paramCollection.AddWithValue("@Id", model.Id);
        },
        returnParameters: null);
    }
    public void SoftDelete(int id)
    {
        string procName = "[dbo].[Jobs_Delete]";

        _data.ExecuteNonQuery(procName, inputParamMapper: delegate (SqlParameterCollection paramCollection)
        {
            paramCollection.AddWithValue("@Id", id);
        }, returnParameters: null);
    }
    public void AddJobSkill(JobSkillAddRequest model)
    {
        string procName = "[dbo].[JobsSkills_Insert]";

        _data.ExecuteNonQuery(procName, inputParamMapper: delegate (SqlParameterCollection paramCollection)
        {
            DataTable skillIdsTable = MapSkillIdsToTable(model.Skills);
            paramCollection.AddWithValue("@JobId", model.JobId);
            paramCollection.AddWithValue("@SkillIds", skillIdsTable);

        },
        returnParameters: null);
    }
    public List<LookUp> GetJobSkillsById(int jobId)
    {
        string procName = "[dbo].[JobsSkills_SelectByJobId]";
        List<LookUp> list = null;
        _data.ExecuteCmd(procName, delegate (SqlParameterCollection paramCollection)
        {
            paramCollection.AddWithValue("@JobId", jobId);
        }, delegate (IDataReader reader, short set)
        {
            int startingIdx = 0;
            LookUp skill = _lookUpService.MapSingleLookUp(reader, ref startingIdx);

            if (list == null)
            {
                list = new List<LookUp>();
            }

            list.Add(skill);
        });
        return list;
    }
    public List<SkillMatch> GetMatchingSkills(int userId, int jobId)
    {
        string procName = "[dbo].[JobsSkills_UserSkills_Match]";
        List<SkillMatch> list = null;
        _data.ExecuteCmd(procName, delegate (SqlParameterCollection paramCollection)
        {
            paramCollection.AddWithValue("@UserId", userId);
            paramCollection.AddWithValue("@JobId", jobId);
        }, delegate (IDataReader reader, short set)
        {
            SkillMatch skillMatch = MapSingleSkillMatch(reader);

            if (list == null)
            {
                list = new List<SkillMatch>();
            }

            list.Add(skillMatch);
        });
        return list;
    }
    public Job GetJobById(int id)
    {
        string procName = "[dbo].[Jobs_Select_ById]";

        Job job = null;

        _data.ExecuteCmd(procName, delegate (SqlParameterCollection parameterCollection)
        {
            parameterCollection.AddWithValue("@Id", id);

        }, delegate (IDataReader reader, short set)
        {
            job = MapSingleJob(reader);
        });
        return job;
    }

    private SkillMatch MapSingleSkillMatch(IDataReader reader)
    {
        SkillMatch skillMatch = new SkillMatch();
        int startingIdx = 0;

        skillMatch.JobSkillId = reader.GetSafeInt32(startingIdx++);
        skillMatch.UserSkillId = reader.GetSafeInt32(startingIdx++);

        return skillMatch;
    }
    public Job MapSingleJob(IDataReader reader)
    {
        Job aJob = new Job();
        int startingIdx = 0;

        aJob.Id = reader.GetInt32(startingIdx++);
        aJob.Type = _lookUpService.MapSingleLookUp(reader, ref startingIdx);
        aJob.Title = reader.GetSafeString(startingIdx++);
        aJob.Description = reader.GetSafeString(startingIdx++);
        aJob.Requirements = reader.GetSafeString(startingIdx++);
        aJob.Location = _locationService.MapSingleLocation(reader, ref startingIdx);
        aJob.IsActive = reader.GetSafeBool(startingIdx++);
        aJob.Contact = reader.GetSafeString(startingIdx++);
        aJob.Phone = reader.GetSafeString(startingIdx++);
        aJob.Email = reader.GetSafeString(startingIdx++);
        aJob.CreatedBy = reader.DeserializeObject<BaseUser>(startingIdx++);
        var dateDB = reader.GetSafeDateTime(startingIdx++);
        aJob.DateCreated = dateDB.ToString("MM/dd/yyyy");
        aJob.DateModified = reader.GetSafeDateTime(startingIdx++);
        aJob.Skills = reader.DeserializeObject<List<LookUp>>(startingIdx++);
        aJob.OrgLogo = reader.GetSafeString(startingIdx++);
        aJob.OrgName = reader.GetSafeString(startingIdx++);
        aJob.OrgDescription = reader.GetSafeString(startingIdx++);

        return aJob;
    }
    private void AddCommonParams(JobAddRequest model, SqlParameterCollection col, int userId)
    {
        
        col.AddWithValue("@CreatedBy", userId);
        col.AddWithValue("@JobType", model.JobTypeId);
        col.AddWithValue("@LocationId", model.LocationId);
        col.AddWithValue("@Title", model.Title);
        col.AddWithValue("@Description", model.Description);
        col.AddWithValue("@Requirements", model.Requirements);
        col.AddWithValue("@ContactName", model.Contact);
        col.AddWithValue("@ContactPhone", model.Phone);
        col.AddWithValue("@ContactEmail", model.Email);
        col.AddWithValue("@BatchSkills", MapSkillIdsToTable(model.Skills));
    }
    private DataTable MapSkillIdsToTable(List<int> skills)
    {
        DataTable table = new DataTable();
        table.Columns.Add("SkillId");
        foreach (int skillId in skills)
        {
            int startingIdx = 0;
            DataRow dataRow = table.NewRow();
            dataRow.SetField(startingIdx++, skillId);
            table.Rows.Add(dataRow);
        }
        return table;
    }
}
***************************************************************************************************
JobApiController
***************************************************************************************************
public class JobApiController : BaseApiController
{
    private IJobService _service = null; 
    private IAuthenticationService<int> _authenticationService = null;
    
    public JobApiController(IJobService service, ILogger<JobApiController> logger, IAuthenticationService<int> authenticationService) : base(logger)
    {
        _service = service;
        _authenticationService = authenticationService;
    }
    [AllowAnonymous]
    [HttpGet]
    public ActionResult<ItemResponse<Paged<Job>>> SelectAll_Paginated(int pageIndex, int pageSize)
    {
        ActionResult result = null;
        try
        {
            Paged<Job> paged = _service.SelectAllPaginated(pageIndex, pageSize);

            if (paged == null)
            {
                result = NotFound404(new ErrorResponse("Application Resource Not Found"));
            }
            else
            {
                ItemResponse<Paged<Job>> response = new ItemResponse<Paged<Job>> { Item = paged };
                result = Ok200(response);
            }
        }
        catch(Exception ex)
        {
            Logger.LogError(ex.ToString());
            result = StatusCode(500, new ErrorResponse(ex.Message.ToString()));
        }
        return result;
    }
    [HttpGet("createdby")]
    public ActionResult<ItemResponse<Paged<Job>>> SelectByCreatedBy_Paginated(int pageIndex, int pageSize)
    {
        ActionResult result = null;
        try
        {
            int currentUser = _authenticationService.GetCurrentUserId();
            Paged<Job> paged = _service.GetByCreateByPaginated(pageIndex, pageSize, currentUser);

            if (paged == null)
            {
                result = NotFound404(new ErrorResponse("Application Resource Not Found"));
            }
            else
            {
                ItemResponse<Paged<Job>> response = new ItemResponse<Paged<Job>> { Item = paged };
                result = Ok200(response);
            }
        }
        catch (Exception ex)
        {
            Logger.LogError(ex.ToString());
            result = StatusCode(500, new ErrorResponse(ex.Message.ToString()));
        }
        return result;
    }
    [AllowAnonymous]
    [HttpGet("search")]
    public ActionResult<ItemResponse<Paged<Job>>> Search_Paginated(int pageIndex, int pageSize, string query)
    {
        ActionResult result = null;
        try
        {
            Paged<Job> paged = _service.SearchPaginated(pageIndex, pageSize, query);

            if (paged == null)
            {
                result = NotFound404(new ErrorResponse("Application Resource Not Found"));
            }
            else
            {
                ItemResponse<Paged<Job>> response = new ItemResponse<Paged<Job>> { Item = paged };
                result = Ok200(response);
            }
        }
        catch (Exception ex)
        {
            Logger.LogError(ex.ToString());
            result = StatusCode(500, new ErrorResponse(ex.Message.ToString()));
        }
        return result;
    }
    [HttpPost]
    public ActionResult<ItemResponse<int>> Create(JobAddRequest model)
    {
        ObjectResult result = null;
        
        try
        {
            int currentUser = _authenticationService.GetCurrentUserId();
            int id = _service.Add(model, currentUser);
            ItemResponse<int> response = new ItemResponse<int> { Item = id };
            result = Created201(response);
        }
        catch(Exception ex)
        {
            Logger.LogError(ex.ToString());
            result = StatusCode(500, new ErrorResponse(ex.Message));
        }
        return result;
    }
    [HttpPut("{id:int}")]
    public ActionResult<ItemResponse<int>> Update(JobUpdateRequest model)
    {
        int sCode = 200;
        BaseResponse response = null;
        
        try
        {
            int userId = _authenticationService.GetCurrentUserId();
            _service.Update(model, userId);
            response = new SuccessResponse();
        }
        catch(Exception ex)
        {
            sCode = 500;
            response = new ErrorResponse(ex.Message);
        }
        return StatusCode(sCode, response);
    }
    [HttpDelete("{id:int}")]
    public ActionResult<SuccessResponse> SoftDelete(int id)
    {
        int sCode = 200;
        BaseResponse response = null;
        try
        {
            _service.SoftDelete(id);
            response = new SuccessResponse();
        }
        catch(Exception ex)
        {
            sCode = 500;
            response = new ErrorResponse(ex.Message);
        }
        return StatusCode(sCode, response);
    }
    [HttpPost("skills")]
    public ActionResult<ItemResponse<int>> AddSkills(JobSkillAddRequest model)
    {
        int sCode = 200;
        BaseResponse response = null;

        try
        {
            _service.AddJobSkill(model);
            response = new SuccessResponse();
        }
        catch (Exception ex)
        {
            sCode = 500;
            response = new ErrorResponse(ex.Message);
        }
        return StatusCode(sCode, response);
    }
    [HttpGet("skills/{id:int}")]
    public ActionResult<ItemResponse<List<LookUp>>> GetJobSkills_ById(int id)
    {
        ActionResult result = null;
        try
        {
            List<LookUp> list = _service.GetJobSkillsById(id);

            if (list == null)
            {
                result = NotFound404(new ErrorResponse("Application Resource Not Found"));
            }
            else
            {
                ItemResponse<List<LookUp>> response = new ItemResponse<List<LookUp>> { Item = list };
                result = Ok200(response);
            }
        }
        catch (Exception ex)
        {
            Logger.LogError(ex.ToString());
            result = StatusCode(500, new ErrorResponse(ex.Message.ToString()));
        }
        return result;
    }
    [HttpGet("skills/match/{jobid:int}")]
    public ActionResult<ItemResponse<List<SkillMatch>>> GetMatchedSkills(int jobId)
    {
        ActionResult result = null;
        try
        {
            int currentUser = _authenticationService.GetCurrentUserId();
            List<SkillMatch> list = _service.GetMatchingSkills(currentUser, jobId);

            if (list == null)
            {
                result = NotFound404(new ErrorResponse("Application Resource Not Found"));
            }
            else
            {
                ItemResponse<List<SkillMatch>> response = new ItemResponse<List<SkillMatch>> { Item = list };
                result = Ok200(response);
            }
        }
        catch (Exception ex)
        {
            Logger.LogError(ex.ToString());
            result = StatusCode(500, new ErrorResponse(ex.Message.ToString()));
        }
        return result;
    }

    [HttpGet("{id:int}")]

    public ActionResult<ItemResponse<Job>> GetId(int id)
    {
        int iCode = 200;
        BaseResponse response = null;
        try
        {
            Job aJob = _service.GetJobById(id);
            if (aJob == null)
            {
                iCode = 404;
                response = new ErrorResponse("Application resource not found.");
            }
            else
            {
                response = new ItemResponse<Job> { Item = aJob };
            }
        }
        catch (Exception ex)
        {
            iCode = 500;
            response = new ErrorResponse($"Generic Error: {ex.Message}");
            base.Logger.LogError(ex.ToString());
        }
        return StatusCode(iCode, response);
    }
}
***************************************************************************************************
Job Data Models
***************************************************************************************************
public class JobAddRequest
{
    [Required]
    [Range(1, int.MaxValue)]
    public int JobTypeId { get; set; }

    [Required]
    [Range(1,int.MaxValue)]
    public int LocationId { get; set; }

    [Required]
    [StringLength(200, ErrorMessage = "Title required", MinimumLength = 2)]
    public string Title { get; set; }

    [Required]
    [StringLength(4000, ErrorMessage = "Description required", MinimumLength = 2)]
    public string Description { get; set; }

    [Required]
    [StringLength(3000, ErrorMessage = "Requirements required", MinimumLength = 2)]
    public string Requirements { get; set; }

    [Required]
    [StringLength(100, ErrorMessage = "Contact required", MinimumLength = 2)]
    public string Contact { get; set; }

    [StringLength(20, ErrorMessage = "Phone required", MinimumLength = 2)]
    public string Phone { get; set; }

    [StringLength(200, ErrorMessage = "Email required", MinimumLength = 2)]
    public string Email { get; set; }

    [Required]
    public List<int> Skills { get; set; }

}
***************************************************************************************************
public class JobSkillAddRequest
{
    [Required]
    [Range(1, int.MaxValue)]
    public int JobId { get; set; }

    [Required]
    public List<int> Skills { get; set; }
}
***************************************************************************************************
public class JobUpdateRequest : JobAddRequest, IModelIdentifier
{
    public int Id { get; set; }
}
***************************************************************************************************
public class BaseJob
{
    public LookUp Type { get; set; }

    public Location Location { get; set; }

    public string Title { get; set; }

    public string Description { get; set; }

    public string Requirements { get; set; }

    public string Contact { get; set; }
}
***************************************************************************************************
public class Job : BaseJob
{
    public int Id { get; set; }

    public bool IsActive { get; set; }

    public BaseUser CreatedBy { get; set; }

    public string Phone { get; set; }
    
    public string Email { get; set; }
    
    public string DateCreated { get; set; }
    
    public DateTime DateModified { get; set; }
    
    public List<LookUp> Skills { get; set; }
    
    public string OrgLogo { get; set; }
    
    public string OrgName { get; set; }
    
    public string OrgDescription { get; set; }
}
***************************************************************************************************
public class SkillMatch
{
    public int JobSkillId { get; set; }

    public int UserSkillId { get; set; }
}
***************************************************************************************************
