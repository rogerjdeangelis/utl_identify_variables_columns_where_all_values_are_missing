# utl_identify_variables_columns_where_all_values_are_missing
Identify variables columns where all values are missing.   Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    Identify variables columns where all values are missing

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


