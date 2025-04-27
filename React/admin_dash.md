AdmindDashboard
***************************************************************************************************
const AdminDashboard = () => {
  const [dashDetails, setDashDetails] = useState({
    totalUsers: 0,
    totalOrgs: 0,
    totalJobs: 0,
  });

  const onGetInfoError = (err) => {
    _logger("GetInfoError", err);
  };

  const onGetInfoSuccess = (response) => {
    setDashDetails((prevState) => {
      const newState = { ...prevState };
      newState.totalUsers = response.item.totalUsers;
      newState.totalOrgs = response.item.totalOrgs;
      newState.totalJobs = response.item.totalJobs;
      return newState;
    });
  };

  useEffect(() => {
    adminDashService.getInfo().then(onGetInfoSuccess).catch(onGetInfoError);
  }, []);

  return (
    <div>
      <h1>Admin Dashboard</h1>
      <Analytics dashDetails={dashDetails} />
    </div>
  );
};
export default AdminDashboard;
***************************************************************************************************
Analytics
***************************************************************************************************
const Analytics = ({ dashDetails }) => {
  const _logger = debug.extend("Analytics");

  function nFormatter(num) {
    I didn't write this function so I removed the logic.
  }

  const analyticsSorter = (analyticsArr) => {
    const sortedData = analyticsArr.sort((a, b) =>
      a.dimensionValues[0].value > b.dimensionValues[0].value ? 1 : -1
    );
    return sortedData;
  };

  var difference = function (num1, num2) {
    const percentChange = Math.abs((num1 / num2) * 100);
    return percentChange;
  };
  const [data, setData] = useState({
    todayUsers: "",
    yestUsers: "",
    allUserAnalytics: [],
  });

  const [pageAnalytics, setPageAnalytics] = useState({ allPageAnalytics: [] });
  const [sessionAnalytics, setSessionAnalytics] = useState({
    allSessionAnalytics: [
      {
        name: "Session Duration",
        data: [],
        colors: ["#754ffe"],
      },
      {
        name: "Total Sessions",
        data: [],
      },
      {
        name: "Engaged Sessions",
        data: [],
      },
    ],
  });
  const [cityAnalytics, setCityAnalytics] = useState({ allCityAnalytics: [] });
  const [activeUserAnalytics, setActiveUserAnalytics] = useState({
    allActiveUserAnalytics: [{ data: [] }],
    active1Today: 0,
    active1Yesterday: 0,
    active7Today: 0,
    active7Yesterday: 0,
    active28Today: 0,
    active28Yesterday: 0,
  });
  const [eventAnalytics, setEventAnalytics] = useState({
    storiesStats: { events: 0, percentOfTotal: 0 },
    eventsStats: { events: 0, percentOfTotal: 0 },
    organizationsStats: { events: 0, percentOfTotal: 0 },
    blogsStats: { events: 0, percentOfTotal: 0 },
    coursesStats: { events: 0, percentOfTotal: 0 },
    charitiesStats: { events: 0, percentOfTotal: 0 },
    podcastsStats: { events: 0, percentOfTotal: 0 },
  });
  const [conversionAnalytics, setConversionAnalytics] = useState({
    jobsPosted: { total: 0 },
    jobsApplied: { total: 0 },
    userSignups: { total: 0 },
    orgCreations: { total: 0 },
    storiesSubmitted: { total: 0 },
    courseSignups: { total: 0 },
    totalConversions: 0,
    mostPopularAnalytics: {
      event: { views: 0, title: "" },
      organization: { views: 0, title: "" },
      blog: { views: 0, title: "" },
      course: { views: 0, title: "" },
      charity: { views: 0, title: "" },
      podcast: { views: 0, title: "" },
      job: { views: 0, title: "" },
    },
  });
  const userValue = {
    startDate: "7daysAgo",
    endDate: "yesterday",
    metrics: [
      {
        name: "totalUsers",
      },
    ],
    dimensions: [
      {
        name: "date",
      },
    ],
  };

  const pageViewValue = {
    startDate: "7daysAgo",
    endDate: "today",
    metrics: [
      {
        name: "screenPageViews",
      },
      {
        name: "screenPageViewsPerSession",
      },
    ],
    dimensions: [
      {
        name: "pagePath",
      },
    ],
  };

  const cityValue = {
    startDate: "7daysAgo",
    endDate: "today",
    metrics: [
      {
        name: "activeUsers",
      },
    ],
    dimensions: [
      {
        name: "city",
      },
    ],
  };

  const sessionValue = {
    startDate: "7daysAgo",
    endDate: "yesterday",
    metrics: [
      {
        name: "averageSessionDuration",
      },
      {
        name: "sessions",
      },
      {
        name: "engagedSessions",
      },
    ],
    dimensions: [
      {
        name: "date",
      },
    ],
  };

  const activeUserValue = {
    startDate: "30daysAgo",
    endDate: "today",
    metrics: [
      {
        name: "active1DayUsers",
      },
      {
        name: "active7DayUsers",
      },
      {
        name: "active28DayUsers",
      },
    ],
    dimensions: [
      {
        name: "date",
      },
    ],
  };

  const eventValue = {
    startDate: "7daysAgo",
    endDate: "today",
    metrics: [
      {
        name: "eventCount",
      },
    ],
    dimensions: [
      {
        name: "pagePath",
      },
    ],
  };

  const conversionValue = {
    startDate: "7daysAgo",
    endDate: "today",
    metrics: [
      {
        name: "eventCount",
      },
    ],
    dimensions: [
      {
        name: "eventName",
      },
    ],
  };

  useEffect(() => {
    analyticsService
      .analyticsRequest(userValue)
      .then(userAnalyticsRequestSuccess)
      .catch(userAnalyticsRequestError);
    analyticsService
      .analyticsRequest(pageViewValue)
      .then(allPageAnalyticsRequestSuccess)
      .catch(allPageAnalyticsRequestError);
    analyticsService
      .analyticsRequest(sessionValue)
      .then(sessionAnalyticsRequestSuccess)
      .catch(sessionAnalyticsRequestError);
    analyticsService
      .analyticsRequest(cityValue)
      .then(cityAnalyticsRequestSuccess)
      .catch(cityAnalyticsRequestError);
    analyticsService
      .analyticsRequest(activeUserValue)
      .then(activeUserAnalyticsRequestSuccess)
      .catch(activeUserAnalyticsRequestError);
    analyticsService
      .analyticsRequest(eventValue)
      .then(eventAnalyticsRequestSuccess)
      .catch(eventAnalyticsRequestError);
    analyticsService
      .analyticsRequest(conversionValue)
      .then(conversionAnalyticsRequestSuccess)
      .catch(conversionAnalyticsRequestError);
  }, []);

  const conversionAnalyticsRequestSuccess = (response) => {
    let totalConversions = 0;
    const mapConversions = (conversionStat) => {
      if (
        conversionStat.dimensionValues[0].value === "Job Applied" ||
        conversionStat.dimensionValues[0].value === "Job Added" ||
        conversionStat.dimensionValues[0].value === "User Signup" ||
        conversionStat.dimensionValues[0].value === "Org Created" ||
        conversionStat.dimensionValues[0].value === "Story Submitted" ||
        conversionStat.dimensionValues[0].value === "Course Signup"
      ) {
        totalConversions =
          totalConversions + parseInt(conversionStat.metricValues[0].value);
        const newConversionStat = {
          conversionName: conversionStat.dimensionValues[0].value,
          conversionCount: conversionStat.metricValues[0].value,
        };
        return newConversionStat;
      }
    };

    const mapPopulars = (popularStat) => {
      if (
        popularStat.dimensionValues[0].value.includes("Job Details") ||
        popularStat.dimensionValues[0].value.includes("Org Details") ||
        popularStat.dimensionValues[0].value.includes("Event Details") ||
        popularStat.dimensionValues[0].value.includes("Blog Details") ||
        popularStat.dimensionValues[0].value.includes("Charity Details") ||
        popularStat.dimensionValues[0].value.includes("Podcast Details") ||
        popularStat.dimensionValues[0].value.includes("Course Details")
      ) {
        const popularityStat = {
          name: popularStat.dimensionValues[0].value,
          views: popularStat.metricValues[0].value,
        };
        return popularityStat;
      }
    };

    const conversionData = response.item.map(mapConversions).filter((x) => x);
    const popularityData = response.item.map(mapPopulars).filter((x) => x);
    setConversionAnalytics((prevState) => {
      const newState = { ...prevState };
      const conversionAnalyticsMapper = (conversion) => {
        switch (true) {
          case conversion.conversionName === "Job Applied":
            newState.jobsApplied.total = conversion.conversionCount;
            break;
          case conversion.conversionName === "Job Added":
            newState.jobsPosted.total = conversion.conversionCount;
            break;
          case conversion.conversionName === "User Signup":
            newState.userSignups.total = conversion.conversionCount;
            break;
          case conversion.conversionName === "Org Created":
            newState.orgCreations.total = conversion.conversionCount;
            break;
          case conversion.conversionName === "Story Submitted":
            newState.storiesSubmitted.total = conversion.conversionCount;
            break;
          case conversion.conversionName === "Course Signup":
            newState.courseSignups.total = conversion.conversionCount;
            break;

          default:
            break;
        }
      };
      const popularityAnalyticsMapper = (popularityStat) => {
        switch (true) {
          case popularityStat.name.includes("Job Details") &&
            popularityStat.views > newState.mostPopularAnalytics.job.views:
            newState.mostPopularAnalytics.job.title =
              popularityStat.name.slice(12);
            newState.mostPopularAnalytics.job.views = popularityStat.views;
            break;
          case popularityStat.name.includes("Org Details") &&
            popularityStat.views >
              newState.mostPopularAnalytics.organization.views:
            newState.mostPopularAnalytics.organization.title =
              popularityStat.name.slice(12);
            newState.mostPopularAnalytics.organization.views =
              popularityStat.views;
            break;
          case popularityStat.name.includes("Event Details") &&
            popularityStat.views > newState.mostPopularAnalytics.event.views:
            newState.mostPopularAnalytics.event.title =
              popularityStat.name.slice(14);
            newState.mostPopularAnalytics.event.views = popularityStat.views;
            break;
          case popularityStat.name.includes("Blog Details") &&
            popularityStat.views > newState.mostPopularAnalytics.blog.views:
            newState.mostPopularAnalytics.blog.title =
              popularityStat.name.slice(13);
            newState.mostPopularAnalytics.blog.views = popularityStat.views;
            break;
          case popularityStat.name.includes("Charity Details") &&
            popularityStat.views > newState.mostPopularAnalytics.charity.views:
            newState.mostPopularAnalytics.charity.title =
              popularityStat.name.slice(16);
            newState.mostPopularAnalytics.charity.views = popularityStat.views;
            break;
          case popularityStat.name.includes("Podcast Details") &&
            popularityStat.views > newState.mostPopularAnalytics.podcast.views:
            newState.mostPopularAnalytics.podcast.title =
              popularityStat.name.slice(16);
            newState.mostPopularAnalytics.podcast.views = popularityStat.views;
            break;
          case popularityStat.name.includes("Course Details") &&
            popularityStat.views > newState.mostPopularAnalytics.course.views:
            newState.mostPopularAnalytics.course.title =
              popularityStat.name.slice(15);
            newState.mostPopularAnalytics.course.views = popularityStat.views;
            break;

          default:
            break;
        }
      };
      conversionData.map(conversionAnalyticsMapper);
      popularityData.map(popularityAnalyticsMapper);
      newState.totalConversions = totalConversions;
      return newState;
    });
  };

  const conversionAnalyticsRequestError = (err) => {
    _logger(err);
  };

  const eventAnalyticsRequestSuccess = (response) => {
    let totalEvents = 0;
    const eventMapper = (eventStat) => {
      if (
        eventStat.dimensionValues[0].value === "/share-your-story" ||
        eventStat.dimensionValues[0].value === "/events" ||
        eventStat.dimensionValues[0].value === "/organization/listings" ||
        eventStat.dimensionValues[0].value === "/blog/listing" ||
        eventStat.dimensionValues[0].value === "/courses" ||
        eventStat.dimensionValues[0].value === "/charities" ||
        eventStat.dimensionValues[0].value === "/podcast"
      ) {
        totalEvents = totalEvents + parseInt(eventStat.metricValues[0].value);
        const newEventStat = {
          page: eventStat.dimensionValues[0].value,
          eventCount: eventStat.metricValues[0].value,
        };
        return newEventStat;
      }
    };
    const entityEventsData = response.item.map(eventMapper).filter((x) => x);
    setEventAnalytics((prevState) => {
      const newState = { ...prevState };
      const eventAnalyticsMapper = (eventData) => {
        switch (true) {
          case eventData.page === "/courses":
            newState.coursesStats.events = eventData.eventCount;
            newState.coursesStats.percentOfTotal = difference(
              eventData.eventCount,
              totalEvents
            ).toFixed(2);
            break;
          case eventData.page === "/share-your-story":
            newState.storiesStats.events = eventData.eventCount;
            newState.storiesStats.percentOfTotal = difference(
              eventData.eventCount,
              totalEvents
            ).toFixed(2);
            break;
          case eventData.page === "/events":
            newState.eventsStats.events = eventData.eventCount;
            newState.eventsStats.percentOfTotal = difference(
              eventData.eventCount,
              totalEvents
            ).toFixed(2);
            break;
          case eventData.page === "/organization/listings":
            newState.organizationsStats.events = eventData.eventCount;
            newState.organizationsStats.percentOfTotal = difference(
              eventData.eventCount,
              totalEvents
            ).toFixed(2);
            break;
          case eventData.page === "/blog/listing":
            newState.blogsStats.events = eventData.eventCount;
            newState.blogsStats.percentOfTotal = difference(
              eventData.eventCount,
              totalEvents
            ).toFixed(2);
            break;
          case eventData.page === "/charities":
            newState.charitiesStats.events = eventData.eventCount;
            newState.charitiesStats.percentOfTotal = difference(
              eventData.eventCount,
              totalEvents
            ).toFixed(2);
            break;
          case eventData.page === "/podcast":
            newState.podcastsStats.events = eventData.eventCount;
            newState.podcastsStats.percentOfTotal = difference(
              eventData.eventCount,
              totalEvents
            ).toFixed(2);
            break;

          default:
            break;
        }
      };
      entityEventsData.map(eventAnalyticsMapper);
      _logger(newState, eventAnalytics);
      return newState;
    });
    _logger(entityEventsData);
  };

  const eventAnalyticsRequestError = (err) => {
    _logger(err);
  };

  const activeUserAnalyticsRequestSuccess = (response) => {
    _logger("Active Users", response.item);
    const activeUserData = analyticsSorter(response.item);
    setActiveUserAnalytics((prevState) => {
      const newState = { ...prevState };
      newState.allActiveUserAnalytics = [...prevState.allActiveUserAnalytics];
      newState.allActiveUserAnalytics[0].data = activeUserData.map(
        (x) => x.metricValues[0].value
      );
      newState.active1Today = parseInt(
        activeUserData[activeUserData.length - 2].metricValues[0].value
      );
      newState.active1Yesterday = parseInt(
        activeUserData[activeUserData.length - 3].metricValues[0].value
      );
      newState.active7Today = parseInt(
        activeUserData[activeUserData.length - 2].metricValues[1].value
      );
      newState.active7Yesterday = parseInt(
        activeUserData[activeUserData.length - 3].metricValues[1].value
      );
      newState.active28Today = parseInt(
        activeUserData[activeUserData.length - 2].metricValues[2].value
      );
      newState.active28Yesterday = parseInt(
        activeUserData[activeUserData.length - 3].metricValues[2].value
      );
      return newState;
    });
  };

  const activeUserAnalyticsRequestError = (err) => {
    _logger(err);
  };

  const cityAnalyticsRequestSuccess = (response) => {
    _logger("City", response.item);
    const totalUsers = response.item
      .map((x) => parseInt(x.metricValues[0].value))
      .reduce((a, b) => a + b);
    _logger("City", totalUsers);
    const mapCityStats = (cityStat, index) => {
      if (index < 17) {
        return (
          <tr key={`CityStat ${index}`}>
            <td>{cityStat.dimensionValues[0].value}</td>
            <td className="text-end">{cityStat.metricValues[0].value}</td>
            <td className="text-end ">{`${parseFloat(
              difference(cityStat.metricValues[0].value, totalUsers)
            ).toFixed(2)}%`}</td>
          </tr>
        );
      }
    };
    setCityAnalytics((prevState) => {
      const newState = { ...prevState };
      newState.allCityAnalytics = response.item.map(mapCityStats);
      return newState;
    });
  };

  const cityAnalyticsRequestError = (err) => {
    _logger(err);
  };

  const sessionAnalyticsRequestSuccess = (response) => {
    _logger("Session", response);
    const sortedSessionData = analyticsSorter(response.item);
    const sessionDurationData = sortedSessionData.map((x) => {
      const minutes = x.metricValues[0].value / 60;
      return minutes.toFixed(0);
    });
    const totalSessionData = response.item.map((x) => x.metricValues[1].value);
    const engagedSessionData = response.item.map(
      (x) => x.metricValues[2].value
    );
    setSessionAnalytics((prevState) => {
      const newState = { ...prevState };
      newState.allSessionAnalytics[0].data = sessionDurationData;
      newState.allSessionAnalytics[1].data = totalSessionData;
      newState.allSessionAnalytics[2].data = engagedSessionData;
      return newState;
    });
  };

  const allPageAnalyticsRequestSuccess = (response) => {
    _logger("Page", response.item);
    const pageData = response.item;

    setPageAnalytics((prevState) => {
      const newState = { ...prevState };
      newState.allPageAnalytics = pageData;
      return newState;
    });
  };

  const allPageAnalyticsRequestError = (err) => {
    _logger(err);
  };
  const sessionAnalyticsRequestError = (err) => {
    _logger(err);
  };

  const userAnalyticsRequestSuccess = (response) => {
    _logger("Daily Users", response.item);
    const analyticsData = analyticsSorter(response.item);

    setData((prevState) => {
      let data = { ...prevState };
      data.allUserAnalytics = analyticsData;
      data.todayUsers = nFormatter(
        analyticsData[analyticsData.length - 1].metricValues[0].value
      );
      data.yestUsers = nFormatter(
        analyticsData[analyticsData.length - 2].metricValues[0].value
      );
      return data;
    });
  };

  const userAnalyticsRequestError = (error) => {
    _logger("Error result", error);
  };

  return (
    <Fragment>
      <Row>
        <Col xl={3} lg={6} md={12} sm={12}>
          <StatRightChart
            title="DAILY USERS"
            value={data.todayUsers}
            summary="Number of users"
            summaryValue={
              Math.abs(
                difference(data.todayUsers, data.yestUsers) - 100
              ).toFixed(2) + "%"
            }
            summaryIcon={
              parseInt(data.todayUsers) < parseInt(data.yestUsers)
                ? "down"
                : "up"
            }
            isSummaryIconShown
            classValue=""
            chartName="UserChart"
            allAnalytics={data.allUserAnalytics}
          />
        </Col>
        <Col xl={3} lg={6} md={12} sm={12} className="mb-4">
          <Card border="light">
            <Card.Body>
              <Row>
                <Col md={12} lg={12} xl={12} sm={12}>
                  <span className="fw-semi-bold text-uppercase fs-6">
                    Users
                    <i className="fe fe-user fs-1 float-end bg-primary rounded-circle text-light"></i>
                  </span>
                </Col>
                <Col md={6} lg={6} xl={6} sm={6}>
                  <h1 className="fw-bold mt-3 mb-0 h2">Total:</h1>
                </Col>
                <Col
                  md={6}
                  lg={6}
                  xl={6}
                  sm={6}
                  className="d-flex align-items-center"
                >
                  <span className="fs-1 mt-1">{dashDetails?.totalUsers}</span>
                </Col>
              </Row>
            </Card.Body>
          </Card>
        </Col>

        <Col xl={3} lg={6} md={12} sm={12} className="mb-4">
          <Card border="light">
            <Card.Body>
              <Row>
                <Col md={12} lg={12} xl={12} sm={12}>
                  <span className="fw-semi-bold text-uppercase fs-6">
                    Organizations
                    <i className="fe fe-check fs-1 float-end bg-primary rounded-circle text-light"></i>
                  </span>
                </Col>
                <Col md={6} lg={6} xl={6} sm={6}>
                  <h1 className="fw-bold mt-3 mb-0 h2">Total:</h1>
                </Col>
                <Col
                  md={6}
                  lg={6}
                  xl={6}
                  sm={6}
                  className="d-flex align-items-center"
                >
                  <span className="fs-1 mt-1">{dashDetails?.totalOrgs}</span>
                </Col>
              </Row>
            </Card.Body>
          </Card>
        </Col>

        <Col xl={3} lg={6} md={12} sm={12} className="mb-4">
          <Card border="light">
            <Card.Body>
              <Row>
                <Col md={12} lg={12} xl={12} sm={12}>
                  <span className="fw-semi-bold text-uppercase fs-6">
                    Job Posts
                    <i className="fe fe-bookmark fs-1 float-end bg-primary rounded-circle text-light"></i>
                  </span>
                </Col>
                <Col md={6} lg={6} xl={6} sm={6}>
                  <h1 className="fw-bold mt-3 mb-0 h2">Total:</h1>
                </Col>
                <Col
                  md={6}
                  lg={6}
                  xl={6}
                  sm={6}
                  className="d-flex align-items-center"
                >
                  <span className="fs-1 mt-1">{dashDetails?.totalJobs}</span>
                </Col>
              </Row>
            </Card.Body>
          </Card>
        </Col>
      </Row>

      <Row>
        <Col xl={8} lg={12} md={12} className="mb-4">
          <Card className="h-100">
            <Card.Header className="align-items-center card-header-height d-flex justify-content-between align-items-center">
              <h4 className="mb-0">Sessions </h4>
            </Card.Header>
            <Card.Body>
              {sessionAnalytics.allSessionAnalytics[0].data.length > 0 ? (
                <ApexCharts
                  options={SessionChartOptions}
                  series={sessionAnalytics.allSessionAnalytics}
                  type="line"
                />
              ) : (
                ""
              )}
            </Card.Body>
          </Card>
        </Col>
        <Col xl={4} lg={12} md={12} className="mb-4">
          <Card className="h-100">
            <Card.Header className="align-items-center card-header-height d-flex justify-content-between align-items-center">
              <h4 className="mb-0">Active Users</h4>
            </Card.Header>
            <Card.Body>
              <Row>
                <Col>
                  <span className="fw-semi-bold">28 days</span>
                  <h1 className="fw-bold mt-2 mb-0 h2">
                    {activeUserAnalytics.active28Today}
                  </h1>
                  <p
                    className={
                      activeUserAnalytics.active28Today >=
                      activeUserAnalytics.active28Yesterday
                        ? "text-success fw-semi-bold mb-0"
                        : "text-danger fw-semi-bold mb-0"
                    }
                  >
                    <i
                      className={
                        activeUserAnalytics.active28Today >=
                        activeUserAnalytics.active28Yesterday
                          ? "fe fe-trending-up me-1"
                          : "fe fe-trending-down me-1"
                      }
                    ></i>
                    {Math.abs(
                      difference(
                        activeUserAnalytics.active28Today,
                        activeUserAnalytics.active28Yesterday
                      ) - 100
                    ).toFixed(2)}
                    %
                  </p>
                </Col>
                <Col>
                  <span className="fw-semi-bold">7 days</span>
                  <h1 className="fw-bold mt-2 mb-0 h2">
                    {activeUserAnalytics.active7Today}
                  </h1>
                  <p
                    className={
                      activeUserAnalytics.active28Today >=
                      activeUserAnalytics.active28Yesterday
                        ? "text-success fw-semi-bold mb-0"
                        : "text-danger fw-semi-bold mb-0"
                    }
                  >
                    <i
                      className={
                        activeUserAnalytics.active7Today >=
                        activeUserAnalytics.active7Yesterday
                          ? "fe fe-trending-up me-1"
                          : "fe fe-trending-down me-1"
                      }
                    ></i>
                    {Math.abs(
                      difference(
                        activeUserAnalytics.active7Today,
                        activeUserAnalytics.active7Yesterday
                      ) - 100
                    ).toFixed(2)}
                    %
                  </p>
                </Col>
                <Col>
                  <span className="fw-semi-bold">1 days</span>
                  <h1 className="fw-bold mt-2 mb-0 h2">
                    {activeUserAnalytics.active1Today}
                  </h1>
                  <p
                    className={
                      activeUserAnalytics.active1Today >=
                      activeUserAnalytics.active1Yesterday
                        ? "text-success fw-semi-bold mb-0"
                        : "text-danger fw-semi-bold mb-0"
                    }
                  >
                    <i
                      className={
                        activeUserAnalytics.active1Today >=
                        activeUserAnalytics.active1Yesterday
                          ? "fe fe-trending-up me-1"
                          : "fe fe-trending-down me-1"
                      }
                    ></i>
                    {Math.abs(
                      difference(
                        activeUserAnalytics.active1Today,
                        activeUserAnalytics.active1Yesterday
                      ) - 100
                    ).toFixed(2)}
                    %
                  </p>
                </Col>
              </Row>
              {activeUserAnalytics.allActiveUserAnalytics[0].data.length > 0 ? (
                <ApexCharts
                  options={ActiveUserChartOptions}
                  series={activeUserAnalytics.allActiveUserAnalytics}
                  type="bar"
                />
              ) : (
                ""
              )}
            </Card.Body>
          </Card>
        </Col>
      </Row>

      <Row>
        <Col xl={4} lg={12} md={12} className="mb-4">
          <Card className="h-100">
            <Card.Header className="align-items-center card-header-height d-flex justify-content-between align-items-center">
              <h4 className="mb-0">Users by City</h4>
              <sup className="fs-6 text-muted">Weekly</sup>
            </Card.Header>
            <Card.Body className="py-0">
              <Table borderless size="sm">
                {cityAnalytics.allCityAnalytics.length > 0 ? (
                  <tbody>{cityAnalytics.allCityAnalytics}</tbody>
                ) : (
                  ""
                )}
              </Table>
            </Card.Body>
          </Card>
        </Col>

        <Col xl={4} lg={12} md={12} className="mb-4">
          <Card className="h-100">
            <Card.Header className="align-items-center card-header-height d-flex justify-content-between align-items-center">
              <h4 className="mb-0">User Engagement</h4>
              <sup className="fs-6 text-muted">Weekly</sup>
            </Card.Header>
            <Card.Body className="p-1">
              <ApexCharts
                options={TrafficChannelChartOptions}
                series={TrafficChannelChartSeries(eventAnalytics)}
                type="donut"
                height={260}
              />
              <div className="table-responsive">
                <Table className="w-100 mt-1 text-nowrap" borderless>
                  <thead>
                    <tr>
                      <th scope="col" className="">
                        Page
                      </th>
                      <th scope="col" className="text-end ">
                        Events
                      </th>
                      <th scope="col" className="text-end ">
                        Percent Of Total
                      </th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr>
                      <td className="text-dark fw-medium pt-1">
                        <Icon
                          path={mdiSquareRounded}
                          className="text-info fs-5 me-2"
                          size={0.6}
                        />
                        Stories
                      </td>
                      <td className="text-end fw-semi-bold pt-1 text-dark">
                        {eventAnalytics.storiesStats.events}
                      </td>
                      <td className="text-end pt-1">
                        {eventAnalytics.storiesStats.percentOfTotal}%
                      </td>
                    </tr>
                    <tr>
                      <td className="text-dark fw-medium pt-1">
                        <Icon
                          path={mdiSquareRounded}
                          className="text-secondary fs-5 me-2"
                          size={0.6}
                        />
                        Events
                      </td>
                      <td className="text-end fw-semi-bold  pt-1 text-dark">
                        {eventAnalytics.eventsStats.events}
                      </td>
                      <td className="text-end pt-1">
                        {eventAnalytics.eventsStats.percentOfTotal}%
                      </td>
                    </tr>
                    <tr>
                      <td className="text-dark fw-medium pt-1">
                        <Icon
                          path={mdiSquareRounded}
                          className="text-dark fs-5 me-2"
                          size={0.6}
                        />
                        Organizations
                      </td>
                      <td className="text-end fw-semi-bold  pt-1 text-dark">
                        {eventAnalytics.organizationsStats.events}
                      </td>
                      <td className="text-end pt-1">
                        {eventAnalytics.organizationsStats.percentOfTotal}%
                      </td>
                    </tr>
                    <tr>
                      <td className="text-dark fw-medium pt-1">
                        <Icon
                          path={mdiSquareRounded}
                          className="text-warning fs-5 me-2"
                          size={0.6}
                        />
                        Blogs
                      </td>
                      <td className="text-end fw-semi-bold  pt-1 text-dark">
                        {eventAnalytics.blogsStats.events}
                      </td>
                      <td className="text-end pt-1">
                        {eventAnalytics.blogsStats.percentOfTotal}%
                      </td>
                    </tr>
                    <tr>
                      <td className="text-dark fw-medium py-1">
                        <Icon
                          path={mdiSquareRounded}
                          className="fs-5 me-2"
                          size={0.6}
                          color="#8968fe"
                        />
                        Courses
                      </td>
                      <td className="text-end fw-semi-bold py-1 text-dark">
                        {eventAnalytics.coursesStats.events}
                      </td>
                      <td className="text-end  py-1">
                        {eventAnalytics.coursesStats.percentOfTotal}%
                      </td>
                    </tr>
                    <tr>
                      <td className="text-dark fw-medium py-1">
                        <Icon
                          path={mdiSquareRounded}
                          className="text-success fs-5 me-2"
                          size={0.6}
                        />
                        Charities
                      </td>
                      <td className="text-end fw-semi-bold py-1 text-dark">
                        {eventAnalytics.charitiesStats.events}
                      </td>
                      <td className="text-end  py-1">
                        {eventAnalytics.charitiesStats.percentOfTotal}%
                      </td>
                    </tr>
                    <tr>
                      <td className="text-dark fw-medium py-1">
                        <Icon
                          path={mdiSquareRounded}
                          className="text-danger fs-5 me-2"
                          size={0.6}
                        />
                        Podcasts
                      </td>
                      <td className="text-end fw-semi-bold py-1 text-dark">
                        {eventAnalytics.podcastsStats.events}
                      </td>
                      <td className="text-end py-1">
                        {eventAnalytics.podcastsStats.percentOfTotal}%
                      </td>
                    </tr>
                  </tbody>
                </Table>
              </div>
            </Card.Body>
          </Card>
        </Col>

        <Col xl={4} lg={12} md={12} className="mb-4">
          <Card className="h-100">
            <Card.Header className="align-items-center card-header-height d-flex justify-content-between align-items-center">
              <h4 className="mb-0">Conversions</h4>
              <sup className="fs-6 text-muted">Weekly</sup>
            </Card.Header>
            <Card.Body>
              {conversionAnalytics.totalConversions > 0 ? (
                <ApexCharts
                  options={conversionChartOptions}
                  series={conversionChartSeries(conversionAnalytics)}
                  type="polarArea"
                  height={350}
                />
              ) : (
                ""
              )}
              <div className="mt-4 d-flex justify-content-center">
                <ListGroup as="ul" bsPrefix="list-inline" className="mb-0 row">
                  <ListGroup.Item
                    as="li"
                    bsPrefix="list-inline-item col-6 my-2"
                  >
                    <h5 className="mb-0 d-flex align-items-center fs-6 lh-1">
                      <Icon
                        path={mdiSquareRounded}
                        className="text-info fs-5 me-2"
                        size={0.6}
                      />
                      Job Posted
                    </h5>
                  </ListGroup.Item>
                  <ListGroup.Item
                    as="li"
                    bsPrefix="list-inline-item col-5 my-2"
                  >
                    <h5 className="mb-0 d-flex align-items-center  fs-6 lh-1">
                      <Icon
                        path={mdiSquareRounded}
                        className="text-secondary fs-5 me-2"
                        size={0.6}
                      />
                      Job Applied To
                    </h5>
                  </ListGroup.Item>
                  <ListGroup.Item
                    as="li"
                    bsPrefix="list-inline-item col-6 my-2"
                  >
                    <h5 className="mb-0 d-flex align-items-center  fs-6 lh-1">
                      <Icon
                        path={mdiSquareRounded}
                        className="text-danger fs-5 me-2"
                        size={0.6}
                      />
                      New User Signup
                    </h5>
                  </ListGroup.Item>
                  <ListGroup.Item
                    as="li"
                    bsPrefix="list-inline-item col-5 my-2"
                  >
                    <h5 className="mb-0 d-flex align-items-center  fs-6 lh-1">
                      <Icon
                        path={mdiSquareRounded}
                        className="text-warning fs-5 me-2"
                        size={0.6}
                      />
                      New Org Created
                    </h5>
                  </ListGroup.Item>
                  <ListGroup.Item
                    as="li"
                    bsPrefix="list-inline-item col-6 my-2"
                  >
                    <h5 className="mb-0 d-flex align-items-center  fs-6 lh-1">
                      <Icon
                        path={mdiSquareRounded}
                        className="fs-5 me-2"
                        size={0.6}
                        color="#8968fe"
                      />
                      New Story Submitted
                    </h5>
                  </ListGroup.Item>
                  <ListGroup.Item
                    as="li"
                    bsPrefix="list-inline-item col-5 my-2"
                  >
                    <h5 className="mb-0 d-flex align-items-center  fs-6 lh-1">
                      <Icon
                        path={mdiSquareRounded}
                        className="text-success fs-5 me-2"
                        size={0.6}
                      />
                      User Course Signup
                    </h5>
                  </ListGroup.Item>
                  <ListGroup.Item
                    as="li"
                    bsPrefix="list-inline-item col-12 mt-4"
                  >
                    <h5 className="mb-0 d-flex align-items-center fs-2 lh-1">
                      Total: {nFormatter(conversionAnalytics.totalConversions)}
                    </h5>
                  </ListGroup.Item>
                </ListGroup>
              </div>
            </Card.Body>
          </Card>
        </Col>
      </Row>

      <Row>
        <Col xl={4} lg={12} md={12} className="mb-4 mx-auto">
          <MostPopular
            title="Most Popular"
            mostPopularAnalytics={conversionAnalytics.mostPopularAnalytics}
          />
        </Col>

        <Col xl={4} lg={12} md={12} className="mb-4 mx-auto">
          <MostViewPages
            title="Most View Pages"
            pageAnalytics={pageAnalytics.allPageAnalytics}
          />
        </Col>
      </Row>
      <Row>
        <Col xl={12} lg={12} md={12} className="mb-4">
          <Card className="h-100">
            <Card.Header className="align-items-center card-header-height d-flex justify-content-between align-items-center">
              <h4 className="mb-0">User Management</h4>
            </Card.Header>
            <Card.Body className="p-0">
              <Users />
            </Card.Body>
          </Card>
        </Col>
      </Row>
    </Fragment>
  );
};

Analytics.propTypes = {
  dashDetails: PropTypes.shape({
    totalUsers: PropTypes.number.isRequired,
    totalOrgs: PropTypes.number.isRequired,
    totalJobs: PropTypes.number.isRequired,
  }),
};

export default Analytics;
***************************************************************************************************
AdminDashService
***************************************************************************************************
const adminDashService = { endpoint: `${API_HOST_PREFIX}/api/admindash` };

adminDashService.getInfo = () => {
  const config = {
    method: "GET",
    url: adminDashService.endpoint,
    withCredentials: true,
    crossdomain: true,
    headers: { "Content-Type": "application/json" },
  };
  return axios(config).then(onGlobalSuccess).catch(onGlobalError);
};

export default adminDashService;
***************************************************************************************************
AnalyticsService
***************************************************************************************************
const _logger = debug.extend("Analytics Service");

const analyticsService = { endpoint: `${API_HOST_PREFIX}/api/analytics/` };

analyticsService.analyticsRequest = (payload) => {
  _logger("Service Payload", payload);
  const config = {
    method: "POST",
    url: `${analyticsService.endpoint}data`,
    data: payload,
    withCredentials: true,
    crossdomain: true,
    headers: { "Content-Type": "application/json" },
  };
  return axios(config).then(onGlobalSuccess).catch(onGlobalError);
};

export default analyticsService;
***************************************************************************************************