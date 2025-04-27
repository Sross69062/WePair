JobsCardLg
***************************************************************************************************
function JobCardLg(props) {
  const job = props.job;
  _logger(job);

  const onApply = () => {
    I didn't write this function so I removed the logic.
  };

  return (
    <div className="row border border-light bg-white rounded mx-auto my-2 shadow-lg">
      <div className="col-2 my-2">
        <img src={job.orgLogo} alt="broken" className=" h-100 w-100 rounded" />
      </div>
      <div className="col-8">
        <h2>{job.title}</h2>
        <h4>{job.orgName}</h4>
        <h6>{`${job.location.city}, ${job.location.state.name}`}</h6>
      </div>
      <div className="col-2 pt-4">
        {job.hasApplied ? (
          <button disabled={true} className="btn btn-secondary mt-5">
            Applied
          </button>
        ) : (
          <button onClick={onApply} className="btn btn-primary mt-5">
            Apply
          </button>
        )}
      </div>
      <hr />
      <h6 className="col-10">Posted:</h6>
      <p className="col-2">{job.dateCreated.slice(0, 10)}</p>
      <hr />
      <h6 className="col-10">Job Type:</h6>
      <p className="col-2">{job.type.name}</p>
      <hr />
      <h6 className="col-10">Job Location:</h6>
      <p className="col-2">{`${job.location.lineOne} ${job.location.city}, ${job.location.state.name}`}</p>
      <hr />
      {job.skills ? (
        <>
          <h6 className="col-3">
            Matching Skills: <SkillMatchCard job={job} index={0} />
            <br /> Missing Skills: <SkillMatchCard job={job} index={1} />
          </h6>
          <div className="col-9">
            <div className="row">
              <div className="col-12">
                <details className="job-skills-details float-end">
                  <summary>List Of All Job Skills</summary>
                  <SkillsMatchChecklist
                    job={job}
                    mainClass="card shadow-lg border border-light p-2 job-skills-checklist"
                  />
                </details>
              </div>
            </div>
          </div>
          <hr />
        </>
      ) : (
        ""
      )}
      <h2>Description</h2>
      <p>{job.description}</p>
      <hr />
      <h2>Requirements</h2>
      <div dangerouslySetInnerHTML={{ __html: job.requirements }} />
      <hr />
      <h2>Organization</h2>
      <div dangerouslySetInnerHTML={{ __html: job.orgDescription }} />
    </div>
  );
}

JobCardLg.propTypes = {
  job: PropTypes.shape({
    location: PropTypes.shape({
      city: PropTypes.string.isRequired,
      state: PropTypes.shape({ name: PropTypes.string.isRequired }).isRequired,
      lineOne: PropTypes.string.isRequired,
    }).isRequired,
    title: PropTypes.string.isRequired,
    description: PropTypes.string.isRequired,
    requirements: PropTypes.string.isRequired,
    dateCreated: PropTypes.string.isRequired,
    id: PropTypes.number.isRequired,
    hasApplied: PropTypes.bool.isRequired,
    type: PropTypes.shape({ name: PropTypes.string.isRequired }).isRequired,
    skills: PropTypes.arrayOf({ id: PropTypes.number, name: PropTypes.string }),
    matchedSkills: PropTypes.arrayOf({
      jobSkillId: PropTypes.number,
      userSkillId: PropTypes.number,
    }),
    orgLogo: PropTypes.string.isRequired,
    orgName: PropTypes.string.isRequired,
    orgDescription: PropTypes.string.isRequired,
  }),
  onApply: PropTypes.func.isRequired,
};

export default JobCardLg;
***************************************************************************************************
JobCardSm
***************************************************************************************************
function JobCardSm(props) {
  const [cardHovered, setCardHovered] = useState(false);
  const job = props.job;
  const handleClick = () => {
    trackGoogleAnalyticsEvent(
      "Job",
      `Job Details ${job.title}`,
      window.location.pathname
    );
    _logger(job);
    props.handleClick(job);
  };

  const _logger = debug.extend("jobcardsm");

  const controlHover = () => {
    setCardHovered((prevState) => {
      if (prevState === false) {
        return !prevState;
      }
    });
  };
  const controlLeave = () => {
    setCardHovered((prevState) => {
      if (prevState === true) {
        return !prevState;
      }
    });
  };

  const onApply = () => {
    I didn't write this function so I removed the logic.
  };

  return (
    <React.Fragment>
      <div
        className={
          cardHovered
            ? "card shadow-lg bg-primary text-white mt-2"
            : "mt-2 card"
        }
        id={job.id}
        onMouseEnter={controlHover}
        onMouseLeave={controlLeave}
        onClick={handleClick}
      >
        <div className="row">
          <div className="col-2 mx-2">
            <img
              src={job.orgLogo}
              alt="broken"
              className="h-100 w-100 rounded-circle mt-1"
            />
          </div>
          <div className="col-7 mt-2">
            <h4>{job.title}</h4>
            <h6>{job.orgName}</h6>
            <p>{`${job.location.city}, ${job.location.state.name}`}</p>
          </div>
          <div className="col-2">
            <p className="mt-4">{job.type.name}</p>
          </div>
        </div>
        <div className="card-body">
          <h5 className="card-title col-12">Description</h5>
          <p className="card-text job-smallcarddescription">
            {job.description}
          </p>
          <div className="row">
            <div className="col-9 mt-2">
              <p>Posted: {job.dateCreated}</p>
            </div>
            <div className="col-3">
              {job.hasApplied ? (
                <button disabled={true} className="btn btn-secondary">
                  Applied
                </button>
              ) : (
                <button onClick={onApply} className="btn btn-primary">
                  Apply Today
                </button>
              )}
            </div>
          </div>
        </div>
      </div>
      <hr />
    </React.Fragment>
  );
}

JobCardSm.propTypes = {
  job: PropTypes.shape({
    location: PropTypes.shape({
      city: PropTypes.string.isRequired,
      state: PropTypes.shape({ name: PropTypes.string.isRequired }).isRequired,
    }).isRequired,
    title: PropTypes.string.isRequired,
    description: PropTypes.string.isRequired,
    dateCreated: PropTypes.string.isRequired,
    id: PropTypes.number.isRequired,
    hasApplied: PropTypes.bool,
    type: PropTypes.shape({ name: PropTypes.string }),
    orgLogo: PropTypes.string.isRequired,
    orgName: PropTypes.string.isRequired,
  }),
  handleClick: PropTypes.func.isRequired,
  onApply: PropTypes.func.isRequired,
};

export default JobCardSm;
***************************************************************************************************
Job Cards Css
***************************************************************************************************
.job-card {
    overflow-y: scroll;
    max-height: 750px;
}

.job-card::-webkit-scrollbar {
    display: none;
}

.job-smallcarddescription {
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
}

.job-headLine {
    justify-content: end;
}

.job-search {
    margin-left: 40px;
}

.job-skills-details {
    max-height: 50px;
}

.job-custom-hr {
    height: 1px;
}

.job-skills-checklist {
    z-index: 3;
}
***************************************************************************************************
JobForm
***************************************************************************************************
function JobForm() {
  const [formData, setFormData] = useState({
    id: "",
    jobTypeId: 0,
    locationId: 257,
    title: "",
    description: "",
    requirements: "",
    contact: "",
    phone: "",
    email: "",
    skills: [],
  });

  const nav = useNavigate();

  const [lookUps, setlookUps] = useState({
    jobTypesData: [],
    jobTypesComponents: [],
    skillsData: [],
    skillsComponents: [],
  });
  const [editor, setEditor] = useState(null);

  const _logger = debug.extend("jobForm");

  const { state } = useLocation();
  const mapSkillObjects = (skill) => {
    return { value: skill.id, label: skill.name };
  };

  useEffect(() => {
    lookUpService
      .lookUp(["JobTypes", "Skills"])
      .then(onLookUpSuccess)
      .catch(onLookUpError);
    if (state?.type === "JOB_VIEW") {
      setFormData((prevState) => {
        const newState = { ...prevState };
        newState.id = state.payload.id;
        newState.jobTypeId = state.payload.type.id;
        newState.locationId = state.payload.location.id;
        newState.title = state.payload.title;
        newState.description = state.payload.description;
        newState.requirements = state.payload.requirements;
        newState.contact = state.payload.contact;
        newState.phone = state.payload.phone;
        newState.email = state.payload.email;
        newState.skills = state.payload.skills.map(mapSkillObjects);
        return newState;
      });
    }
  }, [state]);

  const onLookUpSuccess = (response) => {
    const jobTypes = response.item.jobTypes;
    const skillsData = response.item.skills.map(mapSkillObjects);
    setlookUps((prevState) => {
      let newState = {
        ...prevState,
        jobTypesData: jobTypes,
        skillsData: skillsData,
      };
      newState.jobTypesComponents = jobTypes.map(helper.mapLookUpItem);
      return newState;
    });
  };

  const onLookUpError = (error) => {
    _logger("OnLookUpError", error, editor);
  };

  const onAddJobSuccess = (response) => {
    trackGoogleAnalyticsEvent(
      "Job Form",
      "Job Added",
      window.location.pathname
    );
    _logger("Job Added", response);
    toastr.success("Job Posted");
    nav("/organization/jobposts");
  };

  const onAddJobError = (error) => {
    _logger(error);
  };

  const onEditSuccess = (response) => {
    toastr.success("Edit Successful");
    _logger("Job Edited", response);
    nav("/organization/jobposts");
  };

  const onEditError = (err) => {
    _logger(err);
  };

  const handleSubmit = (values) => {
    values.jobTypeId = parseInt(values.jobTypeId);
    values.skills = values.skills.map((item) => item.value);
    if (values.id > 0) {
      jobService.updateJob(values).then(onEditSuccess).catch(onEditError);
    } else {
      jobService.addJob(values).then(onAddJobSuccess).catch(onAddJobError);
    }
  };

  return (
    <div className="shadow-lg p-3 bg-white w-50 mx-auto my-5">
      <Formik
        enableReinitialize={true}
        initialValues={formData}
        onSubmit={handleSubmit}
        validationSchema={jobSchema}
      >
        {({ values, setFieldValue }) => (
          <Form>
            <h3 className="text-center">
              {formData.id && "Edit Job Information"}
              {!formData.id && "Add A New Job Opportunity"}
            </h3>
            <div className="mb-3">
              <label htmlFor="jobTypeId" className="form-label">
                Job Type
              </label>
              <Field
                component="select"
                name="jobTypeId"
                className="form-control"
              >
                <option value="">Select Job Type</option>
                {lookUps.jobTypesComponents}
              </Field>
              <ErrorMessage
                name="jobTypeId"
                component="div"
                className="has-error"
              />
            </div>
            <div className="mb-3">
              <label htmlFor="title" className="form-label">
                Title
              </label>
              <Field type="text" name="title" className="form-control" />
              <ErrorMessage
                name="title"
                component="div"
                className="has-error"
              />
            </div>
            <div className="mb-3">
              <label htmlFor="description" className="form-label">
                Description
              </label>
              <Field
                component="textarea"
                name="description"
                className="form-control"
              />
              <ErrorMessage
                name="description"
                component="div"
                className="has-error"
              />
            </div>
            <div className="mb-3">
              <label htmlFor="requirements" className="form-label">
                Requirements
              </label>
              <CKEditor
                name="requirements"
                editor={ClassicEditor}
                data={values?.requirements}
                onReady={(editor) => {
                  setEditor(editor);
                }}
                onChange={(event, editor) => {
                  const data = editor.getData();
                  setFieldValue("requirements", data);
                }}
              />
              <ErrorMessage
                name="requirements"
                component="div"
                className="has-error"
              />
            </div>
            <div className="mb-3">
              <label htmlFor="contact" className="form-label">
                Contact
              </label>
              <Field type="text" name="contact" className="form-control" />
              <ErrorMessage
                name="contact"
                component="div"
                className="has-error"
              />
            </div>
            <div className="mb-3">
              <label htmlFor="phone" className="form-label">
                Phone
              </label>
              <Field type="text" name="phone" className="form-control" />
              <ErrorMessage
                name="phone"
                component="div"
                className="has-error"
              />
            </div>
            <div className="mb-3">
              <label htmlFor="email" className="form-label">
                Email
              </label>
              <Field type="email" name="email" className="form-control" />
              <ErrorMessage
                name="email"
                component="div"
                className="has-error"
              />
            </div>
            <div className="mb-3">
              <label htmlFor="skills" className="form-label">
                Skills
              </label>
              <Select
                value={values.skills}
                isMulti
                name="skills"
                options={lookUps.skillsData}
                className="basic-multi-select"
                classNamePrefix="select"
                onChange={(option) => {
                  setFieldValue("skills", option);
                }}
              />
            </div>
            <div className="form-check">
              <button className="btn btn-primary">Submit</button>
            </div>
          </Form>
        )}
      </Formik>
    </div>
  );
}

export default JobForm;
***************************************************************************************************
Jobs
***************************************************************************************************
function Jobs({ currentUser }) {
  const _logger = debug.extend("jobs");
  const nav = useNavigate();

  const [jobs, setJobs] = useState({
    defaultData: [],
    searchData: [],
    filteredData: [],
    smCardcomponents: [],
    bigCardComponent: [],
    jobTypeComponent: [],
    jobSelected: false,
    query: "",
    search: false,
  });

  const [zipCoords, setZipCoords] = useState({ lat: 0, lng: 0 });

  const [pageInfo, setPageInfo] = useState({
    index: 0,
    totalPages: 0,
  });

  const [filters, setFilters] = useState({
    filterByType: "",
    filterByPosted: "",
    filterByDistance: "",
  });

  const [infoShown, setInfoShown] = useState(false);

  const [appliedJobs, setAppliedJobs] = useState([]);

  const mapToCardLg = (job, index) => {
    _logger(job);
    return <JobCardLg job={job} key={index} onApply={onApply} />;
  };

  const mapToCardSm = (job) => {
    return (
      <JobCardSm
        job={job}
        key={job.id}
        handleClick={handleClick}
        onApply={onApply}
      />
    );
  };

  const mapToDDItem = (type) => {
    return (
      <div key={type.id} id={type.name} onClick={handleFilter1}>
        <Dropdown.Item href="#">{type.name}</Dropdown.Item>
      </div>
    );
  };

  const onGetJobsSuccess = (response) => {
    let jobsData = response.item.pagedItems;

    setPageInfo((prevState) => {
      const newPageInfo = { ...prevState };
      newPageInfo.totalPages = response.item.totalPages;
      return newPageInfo;
    });

    jobsData = jobsData.map((item) => {
      if (appliedJobs.includes(item.id)) {
        item.hasApplied = true;
      } else {
        item.hasApplied = false;
      }
      return item;
    });
    setJobs((prevState) => {
      const newState = { ...prevState };
      const pusher = (job) => {
        if (job.id) {
          if (!jobs.search) {
            newState.defaultData.push(job);
          } else {
            newState.searchData.push(job);
          }
        } else {
          newState.smCardcomponents.push(job);
        }
      };
      if (pageInfo.index === 0) {
        if (!jobs.search) {
          newState.defaultData = jobsData;
        } else {
          newState.searchData = jobsData;
        }
        newState.smCardcomponents = jobsData.map(mapToCardSm);
        return newState;
      } else {
        if (!jobs.search) {
          newState.defaultData = [...prevState.defaultData];
        } else {
          newState.searchData = [...prevState.defaultData];
        }
        jobsData.map(pusher);
        newState.smCardcomponents = [...prevState.smCardcomponents];
        const newComponents = jobsData.map(mapToCardSm);
        newComponents.map(pusher);
        return newState;
      }
    });
  };

  const onGetJobsApplied = (response) => {
    I didn't write this function so I removed the logic.
  };

  const onGetJobsAppliedError = (error) => {
    I didn't write this function so I removed the logic.
  };

  const onGetJobsError = (error) => {
    _logger("getJobsError", error);
  };

  const onSearchJobsError = (error) => {
    _logger("onSearchJobsError", error);
    if (error.message.includes("404")) {
      setJobs((prevState) => {
        const newState = { ...prevState };
        newState.smCardcomponents = [];
        return newState;
      });
    }
  };

  const onLookUpSuccess = (response) => {
    const jobTypes = response.item.jobTypes;
    setJobs((prevState) => {
      const newState = { ...prevState };
      newState.jobTypeComponent = jobTypes.map(mapToDDItem);
      newState.search = true;
      return newState;
    });
  };

  const onLookUpError = (err) => {
    _logger("LookupError", err);
  };

  const onFormFieldChange = (event) => {
    const target = event.target;
    const value = target.value;
    const name = target.name;
    setJobs((prevState) => {
      const newState = {
        ...prevState,
      };
      newState[name] = value;
      if (!value) {
        newState.smCardcomponents = prevState.defaultData.map(mapToCardSm);
        newState.search = false;
      }
      return newState;
    });
  };
  useEffect(() => {
    _logger(currentUser);
    jobActivityService
      .getAppliedJobs()
      .then(onGetJobsApplied)
      .catch(onGetJobsAppliedError);
  }, []);

  useEffect(() => {
    if (jobs.query) {
      jobService
        .paginatedSearch(pageInfo.index, 50, jobs.query)
        .then(onGetJobsSuccess)
        .catch(onSearchJobsError);
    } else {
      jobService
        .getAllPaginated(pageInfo.index, 3)
        .then(onGetJobsSuccess)
        .catch(onGetJobsError);
    }
  }, [pageInfo.index, jobs.search, appliedJobs]);

  function applyJob(id, prevState) {
    I didn't write this function so I removed the logic.
  }

  const onAppliedError = (error) => {
    I didn't write this function so I removed the logic.
  };

  const onApplySuccess = (id) => {
    I didn't write this function so I removed the logic.
  };

  const onApply = useCallback((id) => {
   I didn't write this function so I removed the logic.
  }, []);

  const handleSearch = () => {
    if (/^\d{5}(-\d{4})?$/.test(jobs.query)) {
      geocode(RequestType.ADDRESS, jobs.query).then(captureZipCoords);
    } else {
      setZipCoords(() => {
        return { lat: 0, lng: 0 };
      });
    }
    setPageInfo((prevState) => {
      const newPageInfo = { ...prevState };
      newPageInfo.index = 0;
      return newPageInfo;
    });
    lookUpService
      .lookUp(["JobTypes"])
      .then(onLookUpSuccess)
      .catch(onLookUpError);
  };

  const handleClick = useCallback((job) => {
    const responseHandler = (response) => {
      job.matchedSkills = response.item || [];
      setJobs((prevState) => {
        const newState = { ...prevState };
        newState.bigCardComponent = (
          <JobCardLg job={job} key={`LG ${job.id}`} onApply={onApply} />
        );
        newState.jobSelected = true;
        return newState;
      });
    };

    jobService.getMatch(job.id).then(responseHandler).catch(responseHandler);
  }, []);

  const handleScroll = () => {
    if (
      document.getElementById("smCardCont").scrollTop ===
      document.getElementById("smCardCont").scrollHeight - 750
    ) {
      setPageInfo((prevState) => {
        if (prevState.index === prevState.totalPages - 1) {
          return prevState;
        }
        const newState = { ...prevState };
        newState.index = prevState.index + 1;
        return newState;
      });
    }
  };

  const handleToTop = () => {
    document.getElementById("smCardCont").scrollTop = 0;
  };
  const showSearchInfo = () => {
    if (!infoShown) {
      Swal.fire({
        title: "Search By Zip Code",
        text: "When searching by zip code the distance filter will be applied to the coordinates relating to that zip code. Otherwise it will be based on your current location.",
      });
      setInfoShown(() => {
        return true;
      });
    }
  };

  const typeFilterRef = useRef();
  const postedFilterRef = useRef();
  const distanceFilterRef = useRef();
  const captureZipCoords = ({ results }) => {
    setZipCoords((prevState) => {
      const newCoords = { ...prevState };
      newCoords.lat = results[0].geometry.location.lat;
      newCoords.lng = results[0].geometry.location.lng;
      return newCoords;
    });
  };
  const firstFilter = (localFilter) => {
    setJobs((prevState) => {
      const newState = { ...prevState };
      newState.filteredData = prevState.searchData.filter(localFilter);
      newState.smCardcomponents = newState.filteredData.map(mapToCardSm);
      return newState;
    });
  };
  const nextFilter = (localFilter) => {
    setJobs((prevState) => {
      const newState = { ...prevState };
      newState.filteredData = prevState.filteredData.filter(localFilter);
      newState.smCardcomponents = newState.filteredData.map(mapToCardSm);
      return newState;
    });
  };
  const theGreatReset = () => {
    typeFilterRef.current = undefined;
    postedFilterRef.current = undefined;
    distanceFilterRef.current = undefined;
    setJobs((prevState) => {
      const newState = { ...prevState };
      newState.filteredData = [];
      newState.smCardcomponents = prevState.searchData.map(mapToCardSm);
      return newState;
    });

    setFilters((prevState) => {
      const newFilters = { ...prevState };
      newFilters.filterByDistance = "";
      newFilters.filterByType = "";
      newFilters.filterByPosted = "";
      return newFilters;
    });
  };
  const todaysDate = new Date().toLocaleDateString();
  const newDate = new Date();
  const weekAgo = new Date(
    newDate.getTime() - 7 * 24 * 60 * 60 * 1000
  ).toLocaleDateString();
  const thirtyDaysAgo = new Date(
    newDate.getTime() - 30 * 24 * 60 * 60 * 1000
  ).toLocaleDateString();
  const compare2Dates = (d1, d2) => {
    let date1 = new Date(d1).getTime();
    let date2 = new Date(d2).getTime();
    return date1 === date2;
  };
  const compare3Dates = (d1, d2, d3) => {
    let date1 = new Date(d1).getTime();
    let date2 = new Date(d2).getTime();
    let date3 = new Date(d3).getTime();
    return date1 <= date2 && date1 >= date3;
  };
  let myPosition = navigator.geolocation.getCurrentPosition((position) => {
    myPosition = {
      lat: position.coords.latitude,
      lng: position.coords.longitude,
    };
  });
  function arePointsNear(checkPoint, centerPoint, mi) {
    var miY = 24901 / 360;
    var miX = Math.cos((Math.PI * centerPoint.lat) / 180.0) * miY;
    var dx = Math.abs(centerPoint.lng - checkPoint.lng) * miX;
    var dy = Math.abs(centerPoint.lat - checkPoint.lat) * miY;
    return Math.sqrt(dx * dx + dy * dy) <= mi;
  }

  const handleFilter1 = (e) => {
    const target = e.currentTarget.id;
    const localFilter = (job) => {
      return job.type.name === target;
    };

    if (!typeFilterRef.current) {
      if (!postedFilterRef.current && !distanceFilterRef.current) {
        firstFilter(localFilter);
      } else {
        nextFilter(localFilter);
      }
      typeFilterRef.current = "change";
      setFilters((prevState) => {
        const newFilters = { ...prevState };
        newFilters.filterByType = target;
        return newFilters;
      });
    }
  };

  const handleFilter2 = (e) => {
    const target = e.currentTarget.id;

    const localFilter = (job) => {
      if (target.includes("Today")) {
        return compare2Dates(job.dateCreated, todaysDate);
      } else if (target.includes("7")) {
        return compare3Dates(job.dateCreated, todaysDate, weekAgo);
      } else if (target.includes("30")) {
        return compare3Dates(job.dateCreated, todaysDate, thirtyDaysAgo);
      }
    };

    if (!postedFilterRef.current) {
      if (!typeFilterRef.current && !distanceFilterRef.current) {
        firstFilter(localFilter);
      } else {
        nextFilter(localFilter);
      }
      postedFilterRef.current = "change";
      setFilters((prevState) => {
        const newFilters = { ...prevState };
        newFilters.filterByPosted = target;
        return newFilters;
      });
    }
  };

  const handleFilter3 = (e) => {
    const target = e.currentTarget.id;
    const miles = parseInt(target.slice(0, 2));
    const localFilter = (job) => {
      const coordObj = {
        lat: job.location.latitude,
        lng: job.location.longitude,
      };
      return arePointsNear(
        coordObj,
        zipCoords.lat ? zipCoords : myPosition,
        miles
      );
    };
    if (!distanceFilterRef.current) {
      if (!typeFilterRef.current && !postedFilterRef.current) {
        firstFilter(localFilter);
      } else {
        nextFilter(localFilter);
      }
      distanceFilterRef.current = "change";
      setFilters((prevState) => {
        const newFilters = { ...prevState };
        newFilters.filterByDistance = target;
        return newFilters;
      });
    }
  };

  return (
    <React.Fragment>
      <Row className="mx-5">
        <Col xxl={4} xl={6} lg={12} xs={12} className="p-0">
          <h1 className="text-secondary">Discover Your New Path Today</h1>
        </Col>

        {jobs.search ? (
          <Col xxl={5} xl={6} lg={12} xs={12}>
            <Row className="mt-2">
              <div className="col-3">
                <Dropdown>
                  <Dropdown.Toggle
                    variant="secondary"
                    id="typeFilter"
                    disabled={typeFilterRef.current}
                  >
                    {filters.filterByType ? filters.filterByType : "Job Type"}
                  </Dropdown.Toggle>
                  <Dropdown.Menu>{jobs.jobTypeComponent}</Dropdown.Menu>
                </Dropdown>
              </div>
              <div className="col-3">
                <Dropdown>
                  <Dropdown.Toggle
                    variant="secondary"
                    id="dateFilter"
                    disabled={postedFilterRef.current}
                  >
                    {filters.filterByPosted ? filters.filterByPosted : "Posted"}
                  </Dropdown.Toggle>
                  <Dropdown.Menu>
                    <Dropdown.Item
                      href="#"
                      id={`Today`}
                      onClick={handleFilter2}
                    >
                      Today
                    </Dropdown.Item>
                    <Dropdown.Item
                      href="#"
                      id={`7-Days`}
                      onClick={handleFilter2}
                    >
                      This Week
                    </Dropdown.Item>
                    <Dropdown.Item
                      href="#"
                      id={`30-Days`}
                      onClick={handleFilter2}
                    >
                      This Month
                    </Dropdown.Item>
                  </Dropdown.Menu>
                </Dropdown>
              </div>
              <div className="col-3">
                <Dropdown>
                  <Dropdown.Toggle
                    variant="secondary"
                    id="radiusFilter"
                    disabled={distanceFilterRef.current}
                  >
                    {filters.filterByDistance
                      ? filters.filterByDistance
                      : "Distance"}
                  </Dropdown.Toggle>
                  <Dropdown.Menu>
                    <Dropdown.Item href="#" id={`10mi`} onClick={handleFilter3}>
                      10 Miles
                    </Dropdown.Item>
                    <Dropdown.Item href="#" id={`25mi`} onClick={handleFilter3}>
                      25 Miles
                    </Dropdown.Item>
                    <Dropdown.Item href="#" id={`50mi`} onClick={handleFilter3}>
                      50 Miles
                    </Dropdown.Item>
                  </Dropdown.Menu>
                </Dropdown>
              </div>
              <div className="col-3">
                <button
                  className="btn btn-secondary"
                  disabled={
                    !typeFilterRef.current &&
                    !distanceFilterRef.current &&
                    !postedFilterRef.current
                  }
                  onClick={theGreatReset}
                >
                  Reset Filters
                </button>
              </div>
            </Row>
          </Col>
        ) : (
          <Col xxl={5} xl={6} lg={12} xs={12}></Col>
        )}
        <Col xxl={3} xl={6} lg={8} xs={12} className="mt-2 p-0">
          <Row>
            <div className="col-2">
              <button
                className="btn btn-secondary"
                onClick={handleSearch}
                disabled={!jobs.query}
              >
                Search
              </button>
            </div>
            <div className="col-10">
              <input
                className="form-control"
                name="query"
                onChange={onFormFieldChange}
                onClick={showSearchInfo}
                type="search"
                placeholder="Search by query/Zip code"
              />
            </div>
          </Row>
        </Col>
      </Row>
      <Row className="mx-5">
        <Col
          xxl={4}
          xl={4}
          lg={12}
          xs={12}
          className="my-2 shadow-lg job-card"
          onScroll={handleScroll}
          id="smCardCont"
        >
          {jobs.smCardcomponents}
          {pageInfo.index >= pageInfo.totalPages - 1 &&
            jobs.smCardcomponents.length > 0 && (
              <button className="btn btn-secondary mb-2" onClick={handleToTop}>
                To The Top
              </button>
            )}
        </Col>
        <Col
          xxl={8}
          xl={8}
          lg={12}
          xs={12}
          id="lgCardCont"
          className="my-2 rounded shadow-lg job-card"
        >
          {jobs.jobSelected && jobs.bigCardComponent}
          {!jobs.jobSelected && (
            <h1 className="text-center my-4">
              {jobs.smCardcomponents?.length
                ? "Select A Job To View More Info"
                : "Filtering Or Search Returned No Results"}
            </h1>
          )}
        </Col>
      </Row>
    </React.Fragment>
  );
}

Jobs.propTypes = {
  currentUser: PropTypes.shape({
    email: PropTypes.string,
    id: PropTypes.number,
    isLoggedIn: PropTypes.bool,
  }),
};

export default Jobs;
***************************************************************************************************
OrgJobCard
***************************************************************************************************
function OrgJobCard(props) {
  const aJob = props.job;
  const nav = useNavigate();
  const _logger = debug.extend("OrgJobCard");

  const CustomToggle = React.forwardRef(({ children, onClick }, ref) => (
    <Link
      to=""
      ref={ref}
      onClick={(e) => {
        e.preventDefault();
        onClick(e);
      }}
    >
      {children}
    </Link>
  ));

  const onDeactivateSuccess = () => {
    toastr.success("Job Deactivated");
  };

  const onDeactivateError = (err) => {
    _logger("Deactivate Job Error", err);
  };

  const editClicked = () => {
    const state = { type: "JOB_VIEW", payload: aJob };
    nav(`/jobs/${aJob.id}`, { state });
  };

  const handleDeactivateClicked = () => {
    props.deactivateClicked(aJob);
    jobService.softDelete(aJob.id).then(onDeactivateSuccess).catch(onDeactivateError);
  };

  const ActionMenu = () => {
    return (
      <Dropdown>
        <Dropdown.Toggle as={CustomToggle}>
          <i className="fe fe-more-vertical text-muted"></i>
        </Dropdown.Toggle>
        <Dropdown.Menu align="end">
          <Dropdown.Header>Settings</Dropdown.Header>
          <Dropdown.Item eventKey="1" onClick={editClicked}>
            <i className="fe fe-edit dropdown-item-icon"></i>Edit Details
          </Dropdown.Item>
          <Dropdown.Item eventKey="2" onClick={handleDeactivateClicked}>
            <i className="fe fe-trash dropdown-item-icon"></i>Deactivate
          </Dropdown.Item>
        </Dropdown.Menu>
      </Dropdown>
    );
  };

  const CardHeading = (item) => {
    return (
      <div>
        <h5 className="mb-0">{item.title}</h5>
        <span className="fs-6">{item.type.name}</span>
        <br />
        <span className="text-muted fs-6">Contact: {item.contact}</span>
      </div>
    );
  };

  return (
    <Card className="h-100 shadow-lg">
      <Card.Body>
        {/* heading*/}
        <div className="d-flex align-items-center justify-content-between">
          {CardHeading(aJob)}
          <div className="d-flex align-items-center">
            <ActionMenu />
          </div>
        </div>
      </Card.Body>

      {/* card footer */}
      <Card.Footer className="bg-white p-0">
        <div className="d-flex justify-content-between ">
          <div className="w-50 py-3 px-4 ">
            <h6 className="mb-0 text-muted">Date Posted:</h6>
            <p className="text-dark fs-6 fw-semi-bold mb-0">{aJob.dateCreated.slice(0, 10)}</p>
          </div>
          <div className="border-start w-50 py-3 px-4">
            <h6 className="mb-0 text-muted">Total Applicants:</h6>
            <p className="text-dark fs-6 fw-semi-bold mb-0">##</p>
          </div>
        </div>
      </Card.Footer>
    </Card>
  );
}

OrgJobCard.propTypes = {
  job: PropTypes.shape({
    id: PropTypes.number.isRequired,
    type: PropTypes.shape({ name: PropTypes.string.isRequired }).isRequired,
    dateCreated: PropTypes.string.isRequired,
    title: PropTypes.string.isRequired,
    contact: PropTypes.string.isRequired,
  }),
  children: PropTypes.string,
  onClick: PropTypes.func,
  deactivateClicked: PropTypes.func,
};
export default OrgJobCard;
***************************************************************************************************
OrgJobPosts
***************************************************************************************************
function OrgJobPosts() {
  const _logger = debug.extend("OrgJobPosts");
  const nav = useNavigate();
  const [jobs, setJobs] = useState({
    data: [],
    components: [],
    index: 0,
    totalPages: 0,
  });

  const onPageChange = (page) => {
    setJobs((prevState) => {
      const newState = { ...prevState };
      newState.index = page - 1;
      return newState;
    });
  };

  const deactivateClicked = useCallback((job) => {
    const indexOfDeactivated = jobs.data.findIndex((aJob) => {
      if (aJob.id === job.id) {
        return true;
      }
    });
    setJobs((prevState) => {
      const newState = { ...prevState };
      newState.data.splice(indexOfDeactivated, 1);
      newState.components.splice(indexOfDeactivated, 1);
      return newState;
    });
  }, []);

  const mapToOrgJobCard = (job) => {
    return (
      <Col xxl={4} xl={4} lg={6} xs={12} className="my-4" key={job.id}>
        <OrgJobCard job={job} deactivateClicked={deactivateClicked} />
      </Col>
    );
  };

  const onGetByCreatedBySuccess = (response) => {
    _logger(response);
    const jobsArr = response.item.pagedItems;
    setJobs((prevState) => {
      const newState = { ...prevState };
      newState.data = jobsArr;
      newState.totalPages = response.item.totalPages;
      newState.components = jobsArr.map(mapToOrgJobCard);
      return newState;
    });
  };

  const onGetByCreatedByError = (err) => {
    _logger(err);
  };

  useEffect(() => {
    jobService
      .getByCreatedByPaginated(jobs.index, 9)
      .then(onGetByCreatedBySuccess)
      .catch(onGetByCreatedByError);
  }, [jobs.index]);

  const goToForm = () => {
    nav("/jobs/new");
  };

  return (
    <>
      <div className="row container mx-auto">
        <h1 className="col-10">Your Job Postings</h1>
        <div className="col-2">
          <button className="btn btn-primary mt-3" onClick={goToForm}>
            Add An Opportunity
          </button>
        </div>
      </div>
      <div className="mx-auto container bg-light rounded">
        <Row>{jobs.components}</Row>
        <Pagination
          pageSize={9}
          onChange={onPageChange}
          current={jobs.index + 1}
          locale={locale}
          total={9 * jobs.totalPages}
        />
      </div>
    </>
  );
}

export default OrgJobPosts;
***************************************************************************************************
SkillMatchCard
***************************************************************************************************

function SkillMatchCard(props) {
  const _logger = debug.extend("SkillMatchCard");
  const job = props.job;
  const index = props.index;

  const filterNonMatches = (skill, index) => {
    if (job.matchedSkills[index]?.userSkillId !== skill.id) {
      return true;
    }
  };

  const filterMatches = (skill, index) => {
    if (job.matchedSkills[index]?.userSkillId === skill.id) {
      return true;
    }
  };

  const matchedSkills = job.skills?.filter(filterMatches);
  const unmatchedSkills = job.skills?.filter(filterNonMatches);
  _logger(matchedSkills, unmatchedSkills);

  return (
    <div className="row">
      {index < 1 ? (
        <>
          <div className="col-12">
            <i className="fe fe-check-circle bg-success rounded-circle mx-1"></i>
            {matchedSkills.length}
          </div>
        </>
      ) : (
        <>
          <div className="col-12">
            <i className="fe fe-x-circle bg-danger rounded-circle mx-1"></i>
            {unmatchedSkills.length}
          </div>
        </>
      )}
    </div>
  );
}

SkillMatchCard.propTypes = {
  job: PropTypes.shape({
    location: PropTypes.shape({
      city: PropTypes.string.isRequired,
      state: PropTypes.shape({ name: PropTypes.string.isRequired }).isRequired,
      lineOne: PropTypes.string.isRequired,
    }).isRequired,
    title: PropTypes.string.isRequired,
    description: PropTypes.string.isRequired,
    requirements: PropTypes.string.isRequired,
    dateCreated: PropTypes.string.isRequired,
    id: PropTypes.number.isRequired,
    hasApplied: PropTypes.bool.isRequired,
    type: PropTypes.shape({ name: PropTypes.string.isRequired }).isRequired,
    skills: PropTypes.arrayOf({ id: PropTypes.number, name: PropTypes.string }),
    matchedSkills: PropTypes.arrayOf({
      jobSkillId: PropTypes.number,
      userSkillId: PropTypes.number,
    }),
  }),
  index: PropTypes.number,
};

export default SkillMatchCard;
***************************************************************************************************
SkillsMatchChecklist
***************************************************************************************************
function SkillsMatchChecklist(props) {
  const job = props.job;

  const filterNonMatches = (skill, index) => {
    if (job.matchedSkills[index]?.userSkillId !== skill.id) {
      return true;
    }
  };

  const filterMatches = (skill, index) => {
    if (job.matchedSkills[index]?.userSkillId === skill.id) {
      return true;
    }
  };

  const matchedSkills = job.skills?.filter(filterMatches);
  const unmatchedSkills = job.skills?.filter(filterNonMatches);

  return (
    <div className={props.mainClass}>
      {matchedSkills.map((x) => {
        return (
          <div className="row" key={`matched ${x.id}`}>
            <div className="col-12">
              <i className="fe fe-check-circle bg-success rounded-circle mx-1"></i>
              <u>{x.name}</u>
            </div>
          </div>
        );
      })}
      {unmatchedSkills.map((x) => {
        return (
          <div className="row" key={`unmatched ${x.id}`}>
            <div className="col-12">
              <i className="fe fe-x-circle bg-danger rounded-circle mx-1"></i>
              <u className="text-muted">{x.name}</u>
            </div>
          </div>
        );
      })}
    </div>
  );
}

SkillsMatchChecklist.propTypes = {
  job: PropTypes.shape({
    location: PropTypes.shape({
      city: PropTypes.string.isRequired,
      state: PropTypes.shape({ name: PropTypes.string.isRequired }).isRequired,
      lineOne: PropTypes.string.isRequired,
    }).isRequired,
    title: PropTypes.string.isRequired,
    description: PropTypes.string.isRequired,
    requirements: PropTypes.string.isRequired,
    dateCreated: PropTypes.string.isRequired,
    id: PropTypes.number.isRequired,
    hasApplied: PropTypes.bool.isRequired,
    type: PropTypes.shape({ name: PropTypes.string.isRequired }).isRequired,
    skills: PropTypes.arrayOf({ id: PropTypes.number, name: PropTypes.string }),
    matchedSkills: PropTypes.arrayOf({
      jobSkillId: PropTypes.number,
      userSkillId: PropTypes.number,
    }),
  }),
  mainClass: PropTypes.string.isRequired,
};

export default SkillsMatchChecklist;
***************************************************************************************************
JobSchema
***************************************************************************************************
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
***************************************************************************************************
JobService
***************************************************************************************************
import axios from "axios";
import {
  onGlobalError,
  onGlobalSuccess,
  API_HOST_PREFIX,
} from "./serviceHelpers";

const jobService = { endPoint: `${API_HOST_PREFIX}/api/jobs` };

jobService.getByCreatedByPaginated = (pageIndex, pageSize) => {
  const config = {
    method: "GET",
    url: `${jobService.endPoint}/createdby?pageIndex=${pageIndex}&pageSize=${pageSize}`,
    crossdomain: true,
    withCredentials: true,
    headers: { "Content-Type": "application/json" },
  };
  return axios(config).then(onGlobalSuccess).catch(onGlobalError);
};

jobService.addJob = (payload) => {
  const config = {
    method: "POST",
    url: jobService.endPoint,
    data: payload,
    crossdomain: true,
    withCredentials: true,
    headers: { "Content-Type": "application/json" },
  };
  return axios(config).then(onGlobalSuccess).catch(onGlobalError);
};

jobService.updateJob = (payload) => {
  const config = {
    method: "PUT",
    url: `${jobService.endPoint}/${payload.id}`,
    data: payload,
    crossdomain: true,
    withCredentials: true,
    headers: { "Content-Type": "application/json" },
  };
  return axios(config).then(onGlobalSuccess).catch(onGlobalError);
};

jobService.softDelete = (id) => {
  const config = {
    method: "DELETE",
    url: `${jobService.endPoint}/${id}`,
    crossdomain: true,
    withCredentials: true,
    headers: { "Content-Type": "application/json" },
  };
  return axios(config).then(onGlobalSuccess).catch(onGlobalError);
};

jobService.getAllPaginated = (pageIndex, pageSize) => {
  const config = {
    method: "GET",
    url: `${jobService.endPoint}?pageIndex=${pageIndex}&pageSize=${pageSize}`,
    crossdomain: true,
    withCredentials: true,
    headers: { "Content-Type": "application/json" },
  };
  return axios(config).then(onGlobalSuccess).catch(onGlobalError);
};

jobService.paginatedSearch = (pageIndex, pageSize, query) => {
  const config = {
    method: "GET",
    url: `${jobService.endPoint}/search?pageIndex=${pageIndex}&pageSize=${pageSize}&query=${query}`,
    crossdomain: true,
    withCredentials: true,
    headers: { "Content-Type": "application/json" },
  };
  return axios(config).then(onGlobalSuccess).catch(onGlobalError);
};

jobService.getSkillsById = (jobId) => {
  const config = {
    method: "GET",
    url: `${jobService.endPoint}/skills/${jobId}`,
    crossdomain: true,
    withCredentials: true,
    headers: { "Content-Type": "application/json" },
  };
  return axios(config).then(onGlobalSuccess).catch(onGlobalError);
};

jobService.getMatch = (jobId) => {
  const config = {
    method: "GET",
    url: `${jobService.endPoint}/skills/match/${jobId}`,
    crossdomain: true,
    withCredentials: true,
    headers: { "Content-Type": "application/json" },
  };
  return axios(config).then(onGlobalSuccess).catch(onGlobalError);
};

jobService.getById = (id) => {
  I didn't write this function so I removed the logic.
};

export default jobService;
***************************************************************************************************
OrganizationService
***************************************************************************************************
let getJobsByOrgId = (id, pageIndex, pageSize) => {
  const config = {
    method: "GET",
    url: `${endPoint}/jobs/${id}?PageIndex=${pageIndex}&PageSize=${pageSize}`,
    crossdomain: true,
    withCredentials: true,
    headers: { "Content-Type": "application/json" },
  };
  return axios(config).then(onGlobalSuccess).catch(onGlobalError);
};
***************************************************************************************************