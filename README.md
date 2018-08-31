# utl_identify_variables_columns_where_all_values_are_missing
Identify variables columns where all values are missing.   Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    
    Identify variables columns where all values are missing

    Additional voodoo solution on end

    github
    https://tinyurl.com/yag8dn4y
    https://github.com/rogerjdeangelis/utl_identify_variables_columns_where_all_values_are_missing

    INPUT
    =====

     WORK.HAVE total obs=5

      Obs    VAR1    VAR2    VAR3    VAR4

       1     A000            A000    A000
       2     A000            A000    A000
       3                     A000
       4     A000            A000
       5     A000            A000    A000


     EXAMPLE OUTPUT
     --------------

                 STATUS_
     TABLEVAR       CD      STATUS

       VAR1         0       MIXTURE MISSINGS and NON-MISSING
       VAR2         1       ALL MISSING
       VAR3         0       NO  MISSING
       VAR4         0       MIXTURE MISSINGS and NON-MISSING


    PROCESS
    =======

     data want;

       * get meta data;
       if _n_=0 then do;
          %let rc=%sysfunc(dosubl('
              ods exclude all;
              ods output nlevels=nlevels;
              proc freq data=have nlevels;
              run;quit;
              ods select all;
              /*
              TABLEVAR  NLEVELS  NMISSLEVELS  NNONMISSLEVELS

                VAR1       2          1              1
                VAR2       1          1              0
                VAR3       1          0              1
                VAR4       2          1              1
              */
           '));
       end;

       set nlevels;

       select;
          when ( NMISSLEVELS    = 1 and NLEVELS > 1 )
                do;status_cd=0;status="MIXTURE MISSINGS and NON-MISSING";end;
          when ( NMISSLEVELS    = 0                 )
                do;status_cd=0;status="NO  MISSING";end;
          when ( NNONMISSLEVELS = 0                 )
                do;status_cd=1;status="ALL MISSING";end;
       end;

       drop N:;

     run;quit;


    OUTPUT
    ======

     WORK.WANT total obs=4

                  STATUS_
      TABLEVAR       CD      STATUS

        VAR1         0       MIXTURE MISSINGS and NON-MISSING
        VAR2         1       ALL MISSING
        VAR3         0       NO  MISSING
        VAR4         0       MIXTURE MISSINGS and NON-MISSING

    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;

    data have;
     input (VAR1 VAR2 VAR3 VAR4) ($);
    cards4;
    A000 . A000 A000
    A000 . A000 A000
     . . A000 .
    A000 . A000 .
    A000 . A000 A000
    ;;;;
    run;quit;

    * see process for solution;


    Additional voodoo solution on end

    * __                                            _
     / _|_ __ ___  _ __ ___   __   _____   ___   __| | ___   ___
    | |_| '__/ _ \| '_ ` _ \  \ \ / / _ \ / _ \ / _` |/ _ \ / _ \
    |  _| | | (_) | | | | | |  \ V / (_) | (_) | (_| | (_) | (_) |
    |_| |_|  \___/|_| |_| |_|   \_/ \___/ \___/ \__,_|\___/ \___/

    ;

    Good Point Data Null.

    Pulled this macro out of oto_voodoo

    https://github.com/rogerjdeangelis/voodoo

    I think I have couple more in my archives?


    Good Point Daa_null_

    pulled this out of

    INPUT
    =====

    data have;
     input (VAR1 VAR2 VAR3 VAR4) ($);
    cards4;
    A000 . A000 A000
    A000 . A000 A000
     . . A000 .
    A000 . A000 .
    A000 . A000 A000
    ;;;;
    run;quit;


    PROCESS
    =======

    %_vdo_mispoptbl(lib=work,mem=have);

    %macro _vdo_mispoptbl(lib=&libname,mem=&data);

        /*

          data zipcode;
            set sashelp.zipcode;
          run;quit;

          %let lib=work;
          %let mem=zipcode;
        */

        title1 "Missing vs Populated Frequencies";

        Proc format;
             value mispopn
              . = 'MIS'
              other='POP';
        ;
             value $mispopc
              ' ' = 'MIS'
              other='POP'
        ;
        run;

        proc sql noprint;
           select count(*) into :_vdo_popmiscnt trimmed from %str(&lib).%str(&mem)
        ;quit;

        ods exclude all;
        ods output onewayfreqs=_vdo_mispop(keep=table frequency);
        proc freq data=%str(&lib).%str(&mem) ;
        format _character_ $mispopc. _numeric_ mispopn.;
        run;quit;
        ods select all;

        /*
        Up to 40 obs WORK.HSP_QA1_FRQ total obs=21

        Obs    TABLE                FREQUENCY

          1    Table ZIP              41267
          2    Table Y                41267
          3    Table X                41267
          4    Table ZIP_CLASS        11455
          5    Table CITY             41267
        */

        data _vdo_mispop001;
           retain variable pop mis mispct;
           length variable $32;
           keep variable pop mis mispct;
           set _vdo_mispop (rename=FREQUENCY=pop);
           mis=sum(&_vdo_popmiscnt, -1*pop);
           mispct=mis/&_vdo_popmiscnt;
           variable=scan(table,2);
        run;quit;

        proc report data=_vdo_mispop001 nowd;
          cols ( "Populated,  Missing and Missing Frequencies and Percents" variable pop mis mispct);
          define variable /display "Variable"   ;
          define pop      /display "Populated"           format=comma18.;
          define mis      /display "Missing"             format=comma18.;
          define mispct   /display "Missing#Percent"     format=percent10.2;
        run;quit;


    %mend _vdo_mispoptbl;


    %_vdo_mispoptbl(lib=work,mem=have);


    OUTPUT
    ======

    Missing vs Populated Frequencies

                    Populated,  Missing and Missing Frequencies and Percents
                                                 Missing#Pe
      Variable    Populated             Missing       rcent
      VAR1                4                   1     20.00%
      VAR2                _                   5    100.00%
      VAR3                5                   0      0.00%
      VAR4                3                   2     40.00%





