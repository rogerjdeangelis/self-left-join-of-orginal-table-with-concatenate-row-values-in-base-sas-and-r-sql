# self-left-join-of-orginal-table-with-concatenate-row-values-in-base-sas-and-r-sql
Self left join of orginal table with concatenate row values in base sas and r sql
    %let pgm=utl-self-left-join-of-orginal-table-with-concatenate-row-values-in-base-sas-and-r-sql;

    %stop_submission;

    Self left join of orginal table with concatenate row values in base sas and r sql

     TWO SOLUTIONS

         1 r sql
         2 base sas
           Keintz, Mark
           mkeintz@outlook.com
           https://communities.sas.com/t5/user/viewprofilepage/user-id/31461

    github
    https://tinyurl.com/ydcead57
    https://github.com/rogerjdeangelis/self-left-join-of-orginal-table-with-concatenate-row-values-in-base-sas-and-r-sql

    communities.sas
    https://tinyurl.com/5n6v7hp3
    https://communities.sas.com/t5/SAS-Programming/Combine-Rows-into-New-column-and-Keep-Original-Layout/m-p/791620#M253603


    /********************************************************************************************************************/
    /*       INPUT           |              PROCESS                     |                      OUTOUT                   */
    /*       =====           |              =======                     |                      ======                   */
    /*                       |                                          |                SELF JOIN                      */
    /* SD1.HAVE obs=3        | 1 R SQL (self explatory/)                |     LEFT TABLE          RIGHT TABLE RESULT    */
    /*                       | =======                                  | ================  | ========================= */
    /*  KEY ID  NAME TOTAL   |                                          |                   |                       MAX */
    /*                       | proc datasets lib=sd1 nolist nodetails;  | KEY ID NAME TOTAL | CONCAT_ID CONCAT_NAME TOT */
    /*   A  13  Bob    15    |  delete want;                            |                   |                           */
    /*   A  14  Joe    24    | run;quit;                                |   A 13  Bob    15 |  13,14,15 Bob,Joe,Jil  24 */
    /*   A  15  Jil    18    |                                          |   A 14  Joe    24 |  13,14,15 Bob,Joe,Jil  24 */
    /*                       | %utl_rbeginx;                            |   A 15  Jil    18 |  13,14,15 Bob,Joe,Jil  24 */
    /*                       | parmcards4;                              |                                               */
    /* options               | library(haven)                           |                                               */
    /*  validvarname=upcase; | library(sqldf)                           |                                               */
    /* libname sd1 "d:/sd1"; | source("c:/oto/fn_tosas9x.R")            |                                               */
    /* data sd1.have;        | options(sqldf.dll = "d:/dll/sqlean.dll") |                                               */
    /*  input key$           | have<-read_sas("d:/sd1/have.sas7bdat")   |                                               */
    /*        id$            | print(have)                              |                                               */
    /*        name$          | want<-sqldf('                            |                                               */
    /*        total;         |   select                                 |                                               */
    /* cards4;               |      l.key                               |                                               */
    /* A 13 Bob 15           |     ,l.id                                |                                               */
    /* A 14 Joe 24           |     ,l.name                              |                                               */
    /* A 15 Jil 18           |     ,l.total                             |                                               */
    /* ;;;;                  |     ,r.concat_id                         |                                               */
    /* run;quit;             |     ,r.concat_name                       |                                               */
    /*                       |     ,r.maxtot                            |                                               */
    /*                       |   from                                   |                                               */
    /*                       |     have as l                            |                                               */
    /*                       |   left                                   |                                               */
    /*                       |     join                                 |                                               */
    /*                       |    (select                               |                                               */
    /*                       |      key                                 |                                               */
    /*                       |     ,group_concat(id)   as concat_id     |                                               */
    /*                       |     ,group_concat(name) as concat_name   |                                               */
    /*                       |     ,max(total)   as maxtot              |                                               */
    /*                       |     from                                 |                                               */
    /*                       |      have                                |                                               */
    /*                       |     group                                |                                               */
    /*                       |      by key ) as r                       |                                               */
    /*                       |    on                                    |                                               */
    /*                       |      l.key = r.key')                     |                                               */
    /*                       | want                                     |                                               */
    /*                       | fn_tosas9x(                              |                                               */
    /*                       |       inp    = want                      |                                               */
    /*                       |      ,outlib ="d:/sd1/"                  |                                               */
    /*                       |      ,outdsn ="want"                     |                                               */
    /*                       |      )                                   |                                               */
    /*                       | ;;;;                                     |                                               */
    /*                       | %utl_rendx;                              |                                               */
    /*                       |                                          |                                               */
    /*                       | proc print data=sd1.want;                |                                               */
    /*                       | run;quit;                                |                                               */
    /*                       |                                          |                                               */
    /*                       |------------------------------------------------------------------------------------------*/
    /*                       |2 BASE SAS                                |                               NAME_           */
    /*                       |==========                                | KEY ID NAME TOTAL ID_COMB     COMB       MAX  */
    /*                       |                                          |                                               */
    /*                       |data want;                                |  A  13 Bob    15   13,14,15  Bob,Joe,Jil  24  */
    /*                       | if 0 then set sd1.have;                  |  A  14 Joe    24   13,14,15  Bob,Joe,Jil  24  */
    /*                       |                                          |  A  15 Jil    18   13,14,15  Bob,Joe,Jil  24  */
    /*                       | length id_comb $80 name_comb $60 ;       |                                               */
    /*                       | do until (last.key);                     |                                               */
    /*                       |   set sd1.have;                          |                                               */
    /*                       |   by key;                                |                                               */
    /*                       |   id_comb=catx(',',id_comb,id);          |                                               */
    /*                       |   name_comb=catx(',',name_comb,name);    |                                               */
    /*                       |   max=max(total,max);                    |                                               */
    /*                       | end;                                     |                                               */
    /*                       |                                          |                                               */
    /*                       | do until (last.key);                     |                                               */
    /*                       |   set sd1.have;                          |                                               */
    /*                       |   by key;                                |                                               */
    /*                       |   output;                                |                                               */
    /*                       | end;                                     |                                               */
    /*                       |run;                                      |                                               */
    /********************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options
     validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
     input key$
           id$
           name$
           total;
    cards4;
    A 13 Bob 15
    A 14 Joe 24
    A 15 Jil 18
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /* SD1.HAVE total obs=3                                                                                                   */
    /*                                                                                                                        */
    /*  KEY    ID    NAME    TOTAL                                                                                            */
    /*                                                                                                                        */
    /*   A     13    Bob       15                                                                                             */
    /*   A     14    Joe       24                                                                                             */
    /*   A     15    Jil       18                                                                                             */
    /**************************************************************************************************************************/
    /*                    _
    / |  _ __   ___  __ _| |
    | | | `__| / __|/ _` | |
    | | | |    \__ \ (_| | |
    |_| |_|    |___/\__, |_|
                       |_|
    */

    proc datasets lib=sd1 nolist nodetails;
     delete want;
    run;quit;

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    options(sqldf.dll = "d:/dll/sqlean.dll")
    have<-read_sas("d:/sd1/have.sas7bdat")
    print(have)
    want<-sqldf('
      select
         l.key
        ,l.id
        ,l.name
        ,l.total
        ,r.concat_id
        ,r.concat_name
        ,r.maxtot
      from
        have as l
      left
        join
       (select
         key
        ,group_concat(id)   as concat_id
        ,group_concat(name) as concat_name
        ,max(total)   as maxtot
        from
         have
        group
         by key ) as r
       on
         l.key = r.key')
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /* R                                                | SAS                                                                 */
    /*                                                  |                                CONCAT_                              */
    /*   KEY ID NAME TOTAL concat_id concat_name maxtot |  KEY    ID    NAME    TOTAL       ID       CONCAT_NAME    MAXTOT    */
    /*                                                  |                                                                     */
    /* 1   A 13  Bob    15  13,14,15 Bob,Joe,Jil     24 |   A     13    Bob       15     13,14,15    Bob,Joe,Jil      24      */
    /* 2   A 14  Joe    24  13,14,15 Bob,Joe,Jil     24 |   A     14    Joe       24     13,14,15    Bob,Joe,Jil      24      */
    /* 3   A 15  Jil    18  13,14,15 Bob,Joe,Jil     24 |   A     15    Jil       18     13,14,15    Bob,Joe,Jil      24      */
    /**************************************************************************************************************************/

    /*___    _
    |___ \  | |__   __ _ ___  ___   ___  __ _ ___
      __) | | `_ \ / _` / __|/ _ \ / __|/ _` / __|
     / __/  | |_) | (_| \__ \  __/ \__ \ (_| \__ \
    |_____| |_.__/ \__,_|___/\___| |___/\__,_|___/

    */

    data want;
     if 0 then set sd1.have;

     length id_comb $80 name_comb $60 ;
     do until (last.key);
       set sd1.have;
       by key;
       id_comb=catx(',',id_comb,id);
       name_comb=catx(',',name_comb,name);
       max=max(total,max);
     end;

     do until (last.key);
       set sd1.have;
       by key;
       output;
     end;
    run;

    /**************************************************************************************************************************/
    /*  WORK.WANT total obs=3                                                                                                 */
    /*                                                                                                                        */
    /*   KEY    ID    NAME    TOTAL    ID_COMB      NAME_COMB     MAX                                                         */
    /*                                                                                                                        */
    /*    A     13    Bob       15     13,14,15    Bob,Joe,Jil     24                                                         */
    /*    A     14    Joe       24     13,14,15    Bob,Joe,Jil     24                                                         */
    /*    A     15    Jil       18     13,14,15    Bob,Joe,Jil     24                                                         */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
