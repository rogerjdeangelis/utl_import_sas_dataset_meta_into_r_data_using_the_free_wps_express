# utl_import_sas_dataset_meta_into_r_data_using_the_free_wps_express
Import sas dataset meta into R data using the free WPS express.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.

    Import sas dataset meta into R data using the free WPS express

    github (copy/paste using .sas file not readme)
    https://github.com/rogerjdeangelis/utl_import_sas_dataset_meta_into_r_data_using_the_free_wps_express

    see
    https://stackoverflow.com/questions/50632708/read-sas-file-to-get-meta-information

    Query WPS/SAS dataset dictionary meta data and import results into R

    INPUT
    =====

     WORK.IRIS total obs=150

       KEY    SPECIES    SEPALLENGTH    SEPALWIDTH    PETALLENGTH    PETALWIDTH

         1    Setosa          50            33             14             2
         2    Setosa          46            34             14             3
         3    Setosa          46            36             10             2
         4    Setosa          51            33             17             5
         5    Setosa          55            35             13             2
         6    Setosa          48            31             16             2
       ...


     With the following meta data (WPS meta data may vary)
     ------------------------------------------------------

      Middle Observation(3 ) of WORK.MTACOL - Total Obs 6


       -- CHARACTER --
      LIBNAME                C8       WORK              Library Name
      MEMNAME                C32      IRIS              Member Name
      MEMTYPE                C8       DATA              Member Type
      NAME                   C32      Key               Primary Key
      TYPE                   C4       num               Column Type
      LABEL                  C256     Key               Primary Key
      FORMAT                 C49      4.                Column Format
      INFORMAT               C49      BEST12.           Column Informat
      IDXUSAGE               C9       SIMPLE            Column Index Type
      XTYPE                  C12      num               Extended Type
      NOTNULL                C3       no                Not NULL?
      TRANSCODE              C3       yes               Transcoded?


       -- NUMERIC --
      LENGTH                 N8       8                 Column Length
      NPOS                   N8       8                 Column Position
      VARNUM                 N8       3                 Column Number in Table
      SORTEDBY               N8       1                 Order in Key Sequence
      PRECISION              N8       0                 Precision
      SCALE                  N8       .                 Scale


     WORK.MTATBL total obs=1

       -- CHARACTER --
      LIBNAME              C8       WORK                Library Name
      MEMNAME              C32      IRIS                Member Name
      MEMTYPE              C8       DATA                Member Type
      DBMS_MEMTYPE         C32                          DBMS Member Type
      MEMLABEL             C256     bunch of meta da    Data Set Label
      TYPEMEM              C8       DATA                Data Set Type
      PROTECT              C3       ---                 Type of Password Protection
      COMPRESS             C8       NO                  Compression Routine
      ENCRYPT              C8       NO                  Encryption
      REUSE                C3       no                  Reuse Space
      ATTR                 C3       ON                  Data Set Attributes
      INDXTYPE             C9       SIMPLE              Type of Indexes
      DATAREP              C32      NATIVE              Data Representation
      SORTNAME             C8                           Name of Collating Sequence
      SORTTYPE             C4       W                   Sorting Type
      SORTCHAR             C8       ANSI                Charset Sorted By
      REQVECTOR            C24      "" 3323#    Requirements Vector
      DATAREPNAME          C170     WINDOWS_64          Data Representation Name
      ENCODING             C256     wlatin1  Western    Data Encoding
      AUDIT                C3       no                  Audit Trail Active?
      AUDIT_BEFORE         C3       no                  Audit Before Image?
      AUDIT_ADMIN          C3       no                  Audit Admin Image?
      AUDIT_ERROR          C3       no                  Audit Error Image?
      AUDIT_DATA           C3       no                  Audit Data Image?
      TOTOBS               C16      1                   TOTOBS

       -- NUMERIC --
      CRDATE               N8       1843479734          Date Created
      MODATE               N8       1843479734          Date Modified
      NOBS                 N8       150                 Number of Physical Observations
      OBSLEN               N8       56                  Observation Length
      NVAR                 N8       6                   Number of Variables
      NPAGE                N8       2                   Number of Pages
      FILESIZE             N8       196608              Size of File
      PCOMPRESS            N8       0                   Percent Compression
      BUFSIZE              N8       65536               Bufsize
      DELOBS               N8       0                   Number of Deleted Observations
      NLOBS                N8       150                 Number of Logical Observations
      MAXVAR               N8       11                  Longest variable name
      MAXLABEL             N8       17                  Longest label
      MAXGEN               N8       0                   Maximum number of generations
      GEN                  N8       .                   Generation number
      NUM_CHARACTER        N8       1                   Number of Character Variables
      NUM_NUMERIC          N8       5                   Number of Numeric Variables

    WORK mtaIdx

      LIBNAME              C8       WORK                Library Name
      MEMNAME              C32      IRIS                Member Name
      MEMTYPE              C8       DATA                Member Type
      NAME                 C32      KEY                 Column Name
      IDXUSAGE             C9       SIMPLE              Column Index Type
      INDXNAME             C32      KEY                 Index Name
      NOMISS               C3       no                  Nomiss Option
      UNIQUE               C3       yes                 Unique Option
      TOTOBS               C16      1                   TOTOBS



    PROCESS
    ========

    %utl_submit_wps64('
    libname wrk sas7bdat "%sysfunc(pathname(work))";
    libname hlp  sas7bdat "C:\Progra~1\SASHome\SASFoundation\9.4\core\sashelp";
    libname sd1 "d:/sd1";
    data iris(index=(key/unique) sortedby=key label="bunch of meta data");
       retain key ;
       label key="Primary Key";
       set hlp.iris ;
       key=_n_;
       informat _numeric_ best12.;
       informat species $10.;
       format _numeric_ 4.;
       format species $12.;
    run;quit;
    proc sql;
      create
        table sd1.mtaColWps as
      select
        *
      from
        sashelp.vcolumn
      where
        libname="WORK" and memname="IRIS"
    ;quit;
    proc sql;
      create
        table sd1.mtaTblWps as
      select
        *
      from
        sashelp.vtable
      where
        libname="WORK" and memname="IRIS"
    ;quit;

    proc sql;
      create
        table sd1.mtaIdxWps as
      select
        *
      from
        sashelp.vindex
      where
        libname="WORK" and memname="IRIS"
    ;quit;
    proc r;
    submit;
    source("C:/Program Files/R/R-3.3.2/etc/Rprofile.site", echo=T);
    library(haven);
    mtacol<-read_sas("d:/sd1/mtaColWps.sas7bdat");
    mtatbl<-read_sas("d:/sd1/mtaTblWps.sas7bdat");
    mtaidx<-read_sas("d:/sd1/mtaIdxWps.sas7bdat");
    mtacol;
    mtatbl;
    mtaidx;
    endsubmit;
    run;quit;
    ');


    OUTPUT
    ======

    The WPS System ( R)



    Column meta data
    ----------------

    # A tibble: 6 x 18
      libname memname memtype        name  type length  npos varnum             label format informat
        <chr>   <chr>   <chr>       <chr> <chr>  <dbl> <dbl>  <dbl>             <chr>  <chr>    <chr>
    1    WORK    IRIS    DATA         key   num      8     0      1       Primary Key     4.  BEST12.
    2    WORK    IRIS    DATA     Species  char     10     8      2      Iris Species   $12.     $10.
    3    WORK    IRIS    DATA SepalLength   num      8    18      3 Sepal Length (mm)     4.  BEST12.
    4    WORK    IRIS    DATA  SepalWidth   num      8    26      4  Sepal Width (mm)     4.  BEST12.
    5    WORK    IRIS    DATA PetalLength   num      8    34      5 Petal Length (mm)     4.  BEST12.
    6    WORK    IRIS    DATA  PetalWidth   num      8    42      6  Petal Width (mm)     4.  BEST12.

      libname memname memtype        name  idxusage sortedby xtype notnull precision scale transcode
        <chr>   <chr>   <chr>       <chr>     <chr>    <dbl> <chr>   <chr>     <dbl> <dbl>     <chr>

    1    WORK    IRIS    DATA         key    SIMPLE        1                      NA    NA       YES
    2    WORK    IRIS    DATA     Species                  0                      NA    NA       YES
    3    WORK    IRIS    DATA SepalLength                  0                      NA    NA       YES
    4    WORK    IRIS    DATA  SepalWidth                  0                      NA    NA       YES
    5    WORK    IRIS    DATA PetalLength                  0                      NA    NA       YES
    6    WORK    IRIS    DATA  PetalWidth                  0                      NA    NA       YES



    Table meta data
    ---------------
    # A tibble: 1 x 26
      libname memname memtype           memlabel typemem     crdate     modate  nobs obslen  nvar
        <chr>   <chr>   <chr>              <chr>   <chr>      <dbl>      <dbl> <dbl>  <dbl> <dbl>

    1    WORK    IRIS    DATA bunch of meta data    DATA 1843481924 1843481924   150     50     6

      libname memname memtype           memlabel filesize pcompress delobs nlobs maxvar maxlabel indxtype
        <chr>   <chr>   <chr>              <chr>    <dbl>     <dbl>  <dbl> <dbl>  <dbl>    <dbl>    <chr>

    1    WORK    IRIS    DATA bunch of meta data    12288        NA      0   150     11       17   SIMPLE
    # ... with 7 more variables:
    datarep<chr>,sortname<chr>,sorttype<chr>,datarepname<chr>,encoding<chr>,num_character<dbl>,num_numeric<dbl>


    Index meta data
    ---------------
    # A tibble: 1 x 9
      libname memname memtype  name idxusage indxname indxpos nomiss unique
        <chr>   <chr>   <chr> <chr>    <chr>    <chr>   <dbl>  <chr>  <chr>
    1    WORK    IRIS    DATA   key   SIMPLE      key      NA           yes

    *                _                _       _
     _ __ ___   __ _| | _____      __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \    / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/   | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|    \__,_|\__,_|\__\__,_|

    ;

    data iris(index=(key/unique) sortedby=key label="bunch of meta data");
       retain key ;
       label key="Primary Key";
       set sashelp.iris ;
       key=_n_;
       informat _numeric_ best12.;
       informat species $10.;
       format _numeric_ 4.;
       format species $12.;
    run;quit;


    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    see process


