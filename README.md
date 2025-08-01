# Error_messages
This repository to SAS Error Documentation Template, A standardized template for documenting SAS system error messages following international documentation standards (ISO/IEC 25010 and IEEE 830).

# SAS SYSTEM ERROR MESSAGE DOCUMENTATION TEMPLATE

## 1. GENERAL ERROR INFORMATION

```sas
| Field | Description | Example |
|-------|-------------|---------|
| Error ID | Unique error identifier | SAS-ERR-001 |
| Error Code | Numeric/alphanumeric code | ERROR 180-322 |
| Category | Error type classification | SYNTAX, DATA, SYSTEM, SECURITY |
| Severity | Criticality level | CRITICAL, HIGH, MEDIUM, LOW |
| SAS Version | Affected system version | SAS 9.4, SAS Viya 3.5 |
| Documentation Date | Creation/update date | 2024-01-15 |
```
## 2. ERROR DESCRIPTION
sas
/* 2.1 Original Message */
ERROR: Variable X not found in dataset WORK.TEST.

/* 2.2 Technical Description */
The referenced variable does not exist in the specified dataset.

/* 2.3 Context of Occurrence */
- Affected Procedures: PROC SQL, DATA STEP, PROC IMPORT
- System Modules: Base SAS, SAS/STAT, SAS/GRAPH
- Prerequisites: Dataset must exist and be accessible


## 3. IMPACT AND CONSEQUENCES
sas
| Aspect | Description |
|---------|-------------|
| System Impact | Process halt, data corruption |
| User Impact | Work loss, task delays |
| Affected Data | Compromised datasets, variables |
| Interrupted Processes | Active procedures |

## 4. DIAGNOSIS AND RESOLUTION
sas
/* 4.1 Diagnostic Steps */
/* Step 1: Review SAS Log */
proc printto log='c:\temp\saslog.txt';
run;

/* Step 2: Check Dataset Contents */
proc contents data=work.test;
run;

/* 4.2 Primary Solution */
data example;
    set input_dataset;
    if missing(var1) then var1 = 0;
run;

/* 4.3 Alternative Solutions */
/* Option 1 */
proc sql;
    select * 
    from dictionary.columns
    where libname='WORK' and memname='TEST';
quit;

/* Option 2 */
%macro check_var(dsn=, var=);
    %local rc;
    %let rc = 0;
    
    proc sql noprint;
        select count(*) into :rc
        from dictionary.columns
        where libname='WORK' and 
              memname=upcase("&dsn") and 
              name=upcase("&var");
    quit;
    
    %if &rc = 0 %then %do;
        %put ERROR: Variable &var not found in &dsn;
    %end;
%mend;

## 5. PRACTICAL EXAMPLES
sas
/* 5.1 Code that Generates Error */
data test;
    set input;
    new_var = non_existent_var + 1;
run;

/* 5.2 Corrected Code */
data test;
    set input;
    if exists(non_existent_var) then
        new_var = non_existent_var + 1;
    else
        new_var = 0;
run;

## 6. ADDITIONAL TECHNICAL INFORMATION
sas
/* Component Dependencies */
libname required "path";
%include required_macros;

/* System Requirements */
* SAS Version: 9.4 or higher;
* Required Memory: 4GB minimum;
* Disk Space: 500MB minimum;
## 7. VALIDATION AND TESTING
sas
/* Test Case 1 */
%macro validate_solution;
    %local test_passed;
    %let test_passed = 0;
    
    data test;
        set sashelp.class;
        if age > 12;
    run;
    
    proc sql noprint;
        select count(*) into :test_passed
        from test;
    quit;
    
    %if &test_passed > 0 %then %put TEST PASSED;
    %else %put TEST FAILED;
%mend;

/* Execute Test */
%validate_solution;
## 8. CHANGE HISTORY
sas
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2024-01-15 | SYSTEM | Initial |
| 1.1 | 2024-01-20 | SYSTEM | Updates |

## 9. CONTACT AND SUPPORT
sas
/* Support Information */
%let support_email = 'sas.support@company.com';
%let emergency_contact = '1-800-XXX-XXXX';
%let documentation_url = 'http://internal.docs/sas-errors';

## 10. EXAMPLE IMPLEMENTATION
sas
/* Complete Error Handling Example */
%macro handle_error;
    %if &syserr ne 0 %then %do;
        %put ERROR: Process failed with code &syserr;
        %put ERROR: See documentation SAS-ERR-001;
        %let error_time = %sysfunc(datetime());
        %let error_user = &sysuserid;
        
        proc sql;
            insert into error_log
            values("&error_time", "&error_user", &syserr,
                   "SAS-ERR-001");
        quit;
        
        %abort cancel;
    %end;
%mend;

/* Usage Example */
data _null_;
    set work.test;
    if missing(required_var) then do;
        %handle_error;
    end;
run;
## 11. REFERENCES
sas
/* Documentation Links */
* SAS Documentation: support.sas.com/documentation
* Internal Wiki: wiki.company.com/sas-errors
* Error Database: errors.company.com/search?id=SAS-ERR-001
