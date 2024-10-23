# RPT101i: SQL I Self-Study Answer Keys

**Exercise 2-1: Writing a Basic SQL Query**

USE Caboodle_Feb

SELECT Name
FROM ProviderDim


**Exercise 2-2: Writing a Multi-Column SQL Query**

USE Caboodle_Feb

SELECT Name,
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; PrimaryDepartment,
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; PrimaryDepartmentEpicId
<br>FROM ProviderDim


**Exercise 3-2: Using an Inner Join**

USE Clarity_Feb

SELECT HSP_ACCOUNT.HSP_ACCOUNT_NAME,
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; HSP_ACCOUNT.HSP_ACCOUNT_ID,
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; HSP_ACCT_DX_LIST.DX_ID,
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; HSP_ACCT_DX_LIST.FINAL_DX_POA_C
<br>FROM HSP_ACCOUNT
	<br>INNER JOIN HSP_ACCT_DX_LIST
		<br>ON HSP_ACCOUNT.HSP_ACCOUNT_ID = HSP_ACCT_DX_LIST.HSP_ACCOUNT_ID


**Exercise 3-4: Adding More Tables**

USE Clarity_Feb

SELECT HSP_ACCOUNT.HSP_ACCOUNT_NAME,
		HSP_ACCOUNT.HSP_ACCOUNT_ID,
		CLARITY_EDG.DX_NAME,
		HSP_ACCT_DX_LIST.FINAL_DX_POA_C
FROM HSP_ACCOUNT
	INNER JOIN HSP_ACCT_DX_LIST
		ON HSP_ACCOUNT.HSP_ACCOUNT_ID = HSP_ACCT_DX_LIST.HSP_ACCOUNT_ID
	INNER JOIN CLARITY_EDG
		ON HSP_ACCT_DX_LIST.DX_ID = CLARITY_EDG.DX_ID


**Exercise 3-5: Aliasing Tables**

USE Clarity_Feb

SELECT PAT_ENC.PAT_ENC_CSN_ID,
		VISIT_PROV.PROV_NAME, --Visit Provider
		PCP.PROV_NAME --PCP
FROM PAT_ENC
	--Join for the Visit Provider
	INNER JOIN CLARITY_SER AS VISIT_PROV
		ON PAT_ENC.VISIT_PROV_ID = VISIT_PROV.PROV_ID
	--Join for the Primary Care Provider
	INNER JOIN CLARITY_SER AS PCP
		ON PAT_ENC.PCP_PROV_ID = PCP.PROV_ID



**Exercise 4-1: Exploring Columns**

USE Caboodle_Feb

EXECUTE SP_COLUMNS BirthFact


**Exercise 4-6: Working with Date/Time Functions**

USE Clarity_Feb

SELECT PAT_ENC_CSN_ID,
	APPT_MADE_DATE,
	CONTACT_DATE,
	DEPARTMENT_ID,
	DATEDIFF(d,APPT_MADE_DATE,CONTACT_DATE) AS "Days from Sched to Enc",
	DATEDIFF(d,CURRENT_TIMESTAMP,CONTACT_DATE) AS "Days to Enc"
FROM PAT_ENC
WHERE CONTACT_DATE > CURRENT_TIMESTAMP


**Exercise 4-7: Convert Numeric Values to Strings with Formatting**

USE Caboodle_Feb

SELECT AppointmentLengthInMinutes AS "Minutes",
	AppointmentLengthInMinutes / 60 AS "Hours",
	AppointmentLengthInMinutes / 60.0 AS "Hours with Decimal",
	STR(AppointmentLengthInMinutes / 60.0, 5, 2) AS "Hours Rounded",
	STR(AppointmentLengthInMinutes / 60.0, 5, 2)+' hr' AS "Hours Formatted"
FROM VisitFact
WHERE AppointmentLengthInMinutes IS NOT NULL


**Exercise 4-10: Using Many Functions Together**

USE Clarity_Feb

SELECT PAT_ID,
	COALESCE(ADMISSION_PROV_ID,'*None Specified') AS "Admission Provider ID",
	COALESCE(DISCHARGE_PROV_ID,'*None Specified') AS "Discharge Provider ID",
	COALESCE(CAST(DEPARTMENT_ID AS varchar),'*None Specified') AS "Department ID",
	CONVERT(varchar,HOSP_ADMSN_TIME,101) AS "Admission Date",
	CONVERT(varchar,HOSP_DISCH_TIME,101) AS "Discharge Date",
	--Count the first day (+1) and every time the date changes (DATEDIFF)
	DATEDIFF(d,HOSP_ADMSN_TIME,HOSP_DISCH_TIME)+1 AS "Number of Dates"
FROM PAT_ENC_HSP

-- RPT101i: SQL I Self-Study Answer Keys
-- Exercise 4-10: Using Many Functions Together (Challenge #1)

USE Clarity_Feb

SELECT PAT_ID,
	COALESCE(ADMISSION_PROV_ID,'*None Specified') AS "Admission Provider ID",
	COALESCE(DISCHARGE_PROV_ID,'*None Specified') AS "Discharge Provider ID",
	COALESCE(CAST(DEPARTMENT_ID AS varchar),'*None Specified') AS "Department ID",
	CONVERT(varchar,HOSP_ADMSN_TIME,101) AS "Admission Date",
	CONVERT(varchar,HOSP_DISCH_TIME,101) AS "Discharge Date",
	--Count the first day (+1) and every time the date changes (DATEDIFF)
	DATEDIFF(d,HOSP_ADMSN_TIME,HOSP_DISCH_TIME)+1 AS "Number of Dates",
	DATEDIFF(hh,HOSP_ADMSN_TIME,HOSP_DISCH_TIME) / 24.0 AS "Length of Stay"
FROM PAT_ENC_HSP

-- RPT101i: SQL I Self-Study Answer Keys
-- Exercise 4-10: Using Many Functions Together (Challenge #2)

USE Clarity_Feb

SELECT PAT_ID,
	COALESCE(ADMISSION_PROV_ID,'*None Specified') AS "Admission Provider ID",
	COALESCE(ADMSN_PROV.PROV_NAME,'*None Specified') AS "Admission Provider",
	COALESCE(DISCHARGE_PROV_ID,'*None Specified') AS "Discharge Provider ID",
	COALESCE(DISCH_PROV.PROV_NAME,'*None Specified') AS "Discharge Provider",
	COALESCE(CAST(DEPARTMENT_ID AS varchar),'*None Specified') AS "Department ID",
	CONVERT(varchar,HOSP_ADMSN_TIME,101) AS "Admission Date",
	CONVERT(varchar,HOSP_DISCH_TIME,101) AS "Discharge Date",
	--Count the first day (+1) and every time the date changes (DATEDIFF)
	DATEDIFF(d,HOSP_ADMSN_TIME,HOSP_DISCH_TIME)+1 AS "Number of Dates",
	DATEDIFF(hh,HOSP_ADMSN_TIME,HOSP_DISCH_TIME)/24.0 AS "Length of Stay"
FROM PAT_ENC_HSP
	LEFT OUTER JOIN CLARITY_SER AS "ADMSN_PROV"
		ON PAT_ENC_HSP.ADMISSION_PROV_ID = ADMSN_PROV.PROV_ID
	LEFT OUTER JOIN CLARITY_SER AS "DISCH_PROV"
		ON PAT_ENC_HSP.DISCHARGE_PROV_ID = DISCH_PROV.PROV_ID
