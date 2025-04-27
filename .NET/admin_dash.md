AnalyticsController
***************************************************************************************************
```C#
public class AnalyticsApiController : BaseApiController
{
    private IAnalyticsService _analyticsService = null;
    public AnalyticsApiController(IAnalyticsService service,ILogger<AnalyticsApiController> logger) : base(logger)
    {
        _analyticsService = service;
    }

    [HttpPost("data")]
    public ActionResult<ItemResponse<Google.Protobuf.Collections.RepeatedField<Row>>> GetGoogleReport(GoogleAnalyticsReportRequest model)
    {
        int iCode = 200;
        BaseResponse response = null;

        try
        {
            Google.Protobuf.Collections.RepeatedField<Row> report = _analyticsService.GetReportResponse(model);
            if (report == null)
            {
                iCode = 404;
                response = new ErrorResponse("Google Analytics information not found");
            }
            else
            {
                response = new ItemResponse<Google.Protobuf.Collections.RepeatedField<Row>>()
                {
                    Item = report
                };
            }
        }
        catch (Exception ex)
        {
            iCode = 500;
            base.Logger.LogError(ex.ToString());
            response = new ErrorResponse(ex.Message);
        }
        return StatusCode(iCode, response);
    }
}
```
***************************************************************************************************
AdminDashController
***************************************************************************************************
```C#
public class AdminDashApiController : BaseApiController
{
    private IAdminDashService _service = null;

    public AdminDashApiController(IAdminDashService service, ILogger<JobApiController> logger) : base(logger)
    {
        _service = service;
    }

    [HttpGet]
    public ActionResult<ItemResponse<AdminDashInfo>> GetInfo()
    {
        ActionResult result = null;

        try
        {
            AdminDashInfo info = _service.GetInfo();

            if (info == null)
            {
                result = NotFound404(new ErrorResponse("Application Resource Not Found"));
            }
            else
            {
                ItemResponse<AdminDashInfo> response = new ItemResponse<AdminDashInfo> { Item = info };
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
}
```
***************************************************************************************************
AdminDashService
***************************************************************************************************
```C#
public class AdminDashService : IAdminDashService
{
    IDataProvider _data = null;

    public AdminDashService(IDataProvider data)
    {
        _data = data;
    }

    public AdminDashInfo GetInfo()
    {
        AdminDashInfo list = null;
        string procName = "[dbo].[AdminDashInfo_Select]";

        _data.ExecuteCmd(procName, null, delegate (IDataReader reader, short set)
        {
            if (list == null)
            {
                list = new AdminDashInfo();
            }
            if (set == 0)
            {
                list.TotalUsers = reader.GetSafeInt32(0);
            }
            else if (set == 1)
            {
                list.TotalOrgs = reader.GetSafeInt32(0);
            }
            else if (set == 2)
            {
                list.TotalJobs = reader.GetSafeInt32(0);
            }
        });

        return list;
    }
}
```
***************************************************************************************************
AnalyticsService
***************************************************************************************************
```C#
public class AnalyticsService : IAnalyticsService
{
    private IWebHostEnvironment _env;
    public AnalyticsService(IWebHostEnvironment env)
    {
        _env = env;

        
        string keyPath = Path.Combine(_env.WebRootPath, "GoogleAnalytics", "google-analytics-credentials.json");

        BetaAnalyticsDataClient client = new BetaAnalyticsDataClientBuilder
        {
            CredentialsPath = keyPath,
        }.Build();
    }

    public Google.Protobuf.Collections.RepeatedField<Row> GetReportResponse(GoogleAnalyticsReportRequest model)
    {
        string keyPath = Path.Combine(_env.WebRootPath, "GoogleAnalytics", "google-analytics-credentials.json");
        string file = File.ReadAllText(keyPath);
        dynamic jsonFile = JsonConvert.DeserializeObject(file);
        string propertyId = jsonFile.property_id;

        BetaAnalyticsDataClient client = new BetaAnalyticsDataClientBuilder
        {
            CredentialsPath = keyPath,
        }.Build();

        RunReportRequest request = new RunReportRequest
        {
            Property = "properties/" + propertyId,
            DateRanges = { new DateRange { StartDate = model.StartDate, EndDate = model.EndDate } },
            Metrics = { model.Metrics },
            Dimensions = { model.Dimensions },
        };

        var response = client.RunReport(request);

        return response.Rows;
    }
}
```
***************************************************************************************************
Admin Dash Data Models
***************************************************************************************************
```C#
public class GoogleAnalyticsReportRequest
{
    public string StartDate { get; set; }

    public string EndDate { get; set; }

    public List<Metric> Metrics { get; set; }

    public List<Dimension> Dimensions { get; set; }
}
```
***************************************************************************************************
```C#
public class AdminDashInfo
{
    public int TotalUsers { get; set; }

    public int TotalOrgs { get; set; }

    public int TotalJobs { get; set; }
}
```
***************************************************************************************************
