# utl-tumbling-goups-of-ten-temperatures-similar-like-rolling-and-moving-means-wps-r-python
    %let pgm=utl-tumbling-goups-of-ten-temperatures-similar-like-rolling-and-moving-means-wps-r-python;

    Tumbling groups of ten and n temperatures similar eolling and moving means wps r python

    github
    https://tinyurl.com/38van5j4
    https://github.com/rogerjdeangelis/utl-tumbling-goups-of-ten-temperatures-similar-like-rolling-and-moving-means-wps-r-python

    https://stackoverflow.com/questions/77017130/rolling-window-slider-r

    Rolling(Tumbling) meas  for sets of 10 observations (related repos on end)

      SOLUTIONS

             1. WPS (decided to go the normalization route)
             2. WPS combination with R SQL
             3. WPS combination with python SQL
             4  Non dql R - Was unable to figure out how to create the R input outside of R

                Not familiar with this input data structure for historicalWeatherSummary

                gropd_df [6 x 3] (S3: grouped_df/tbl_df/tbl/data.frame)
                 $ HISDTE: Date[1:6], format: "1978-12-31" "1979-01-01" "1979-01-02" "1979-01-03" ...
                 $ DAY   : num [1:6] 25.29 33.28 39.33 14.02 -2.47 ...
                 $ NITE  : num [1:6] 25.45 29.82 36.66 6.3 -2.72 ...
                 - attr(*, "groups")= tibble [6 x 2] (S3: tbl_df/tbl/data.frame)
                  ..$ HISDTE: Date[1:6], format: "1978-12-31" "1979-01-01" "1979-01-02" "1979-01-03" ..
                  ..$ .rows : list<int> [1:6]
                     .. ..$ : int 1
                  .. ..$ : int 2
                  .. ..$ : int 3
                  .. ..$ : int 4
                  .. ..$ : int 5
                  .. ..$ : int 6
                  .. ..@ ptype: int(0)
                  ..- attr(*, ".drop")= logi TRUE

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    libname sd1 "d:/sd1";
    options validvarname=upcase;

    data sd1.have;
      input hisday hisdte yymmdd10. dayt nitet;
      format hisdte yymmdd10.;
    cards4;
     1 1978-12-31 20 24
     2 1979-01-01 34 15
     3 1979-01-02 15 24
     4 1979-01-03 12 24
     5 1979-01-04 31 25
     6 1979-01-05 24 13
     7 1979-01-06 14 17
     8 1979-01-07 23 27
     9 1979-01-08 21 13
    10 1979-01-09 14 14
    11 1979-01-10 18 21
    12 1979-01-11 20 26
    13 1979-01-12 28 17
    14 1979-01-13 16 11
    15 1979-01-14 39 14
    16 1979-01-15 28 10
    17 1979-01-16 34 29
    18 1979-01-17 20 14
    19 1979-01-18 22 12
    20 1979-01-19 32 11
    21 1979-01-20 32 25
    22 1979-01-21 26 13
    23 1979-01-22 19 22
    24 1979-01-23 29 26
    25 1979-01-24 16 28
    26 1979-01-25 10 27
    27 1979-01-26 24 21
    28 1979-01-27 18 19
    29 1979-01-28 20 19
    30 1979-01-29 24 18
    31 1979-01-30 34 26
    32 1979-01-31 21 22
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                               |
    /* SD1.HAVE INPUT                | RULES (not the creation of groups)                                  OUTPUT
    /*                               |                                                        ===============================
    /*  HISDAY  HISDTE   DAYT   NITET| HISDAY G1 DAYT   HISDAY  G2 DAYT     HIDDAY G3  DAYT   DTEMIN      AVGDAYT    AVGNITET
    /*                               |
    /*    1   1978-12-31  20     24  |   1    11  20             .              .             1978-12-31      20.8       19.6
    /*    2   1979-01-01  34     15  |   2    11  34         21  4   34         .             1979-01-10      25.7       16.5
    /*    3   1979-01-02  15     24  |   3    11  15         21  4   15         3  31   15    1979-01-20      21.8       21.8
    /*    4   1979-01-03  12     24  |   4    11  12         21  4   12         4  31   12    1979-01-01      20.6       19.3
    /*    5   1979-01-04  31     25  |   5    11  31         21  4   31         5  31   31    1979-01-11      27.1       16.9
    /*    6   1979-01-05  24     13  |   6    11  24         21  4   24         6  31   24    1979-01-21      22.0       21.9
    /*    7   1979-01-06  14     17  |   7    11  14         21  4   14         7  31   14    1979-01-02      19.2       20.4
    /*    8   1979-01-07  23     27  |   8    11  23         21  4   23         8  31   23    1979-01-12      27.7       15.6
    /*    9   1979-01-08  21     13  |   9    11  21         21  4   21         9  31   21    1979-01-22      21.5       22.8
    /*   10   1979-01-09  14     14  |  10    11  14   20.8  21  4   14        10  31   14
    /*   11   1979-01-10  18     21  |  11    12  18         21  4   18  20.6  11  31   18
    /*   12   1979-01-11  20     26  |  12    12  20                           12  31   20 19.2
    /*   13   1979-01-12  28     17  |  13    12  28         22  5   20
    /*   14   1979-01-13  16     11  |  14    12  16         22  5   28        13  32    28
    /*   15   1979-01-14  39     14  |  15    12  39         22  5   16        14  32    16
    /*   16   1979-01-15  28     10  |  16    12  28         22  5   39        15  32    39
    /*   17   1979-01-16  34     29  |  17    12  34         22  5   28        16  32    28
    /*   18   1979-01-17  20     14  |  18    12  20         22  5   34        17  32    34
    /*   19   1979-01-18  22     12  |  19    12  22         22  5   20        18  32    20
    /*   20   1979-01-19  32     11  |  20    12  32  25.7   22  5   22        19  32    22
    /*   21   1979-01-20  32     25  |  21    13  32         22  5   32        20  32    32
    /*   22   1979-01-21  26     13  |  22    13  26         22  5   32  27.1  21  32    32
    /*   23   1979-01-22  19     22  |  23    13  19                           22  32    26 27.7
    /*   24   1979-01-23  29     26  |  24    13  29         23  6   26
    /*   25   1979-01-24  16     28  |  25    13  16         23  6   19        23  33    19
    /*   26   1979-01-25  10     27  |  26    13  10         23  6   29        24  33    29
    /*   27   1979-01-26  24     21  |  27    13  24         23  6   16        25  33    16
    /*   28   1979-01-27  18     19  |  28    13  18         23  6   10        26  33    10
    /*   29   1979-01-28  20     19  |  29    13  20         23  6   24        27  33    24
    /*   30   1979-01-29  24     18  |  30    13  24  21.8   23  6   18        28  33    18
    /*   31   1979-01-30  34     26  |                       23  6   20        29  33    20
    /*   32   1979-01-31  21     22  |                       23  6   24        30  33    24
    /*   33   1979-02-01  11     25  |                       23  6   34 22.0   31  33    34
    /*   34   1979-02-02  38     24  |                                         32  33    21 21.5
    /*   35   1979-02-03  37     19  |
    /*   36   1979-02-04  29     18  |
    /*   37   1979-02-05  29     21  |
    /*                               |
    /**************************************************************************************************************************/

    /*                                                         _ _             _
    / | __      ___ __  ___   _ __   ___  _ __ _ __ ___   __ _| (_)_______  __| |
    | | \ \ /\ / / `_ \/ __| | `_ \ / _ \| `__| `_ ` _ \ / _` | | |_  / _ \/ _` |
    | |  \ V  V /| |_) \__ \ | | | | (_) | |  | | | | | | (_| | | |/ /  __/ (_| |
    |_|   \_/\_/ | .__/|___/ |_| |_|\___/|_|  |_| |_| |_|\__,_|_|_/___\___|\__,_|
                 |_|
    */
    /*---- CREATE GROUP MEANS fot 9 groups 11 12 13 21 22 23 31 32 33        ----*/

    proc datasets lib=sd1 nolist nodetails;delete want; run;quit;

    %utl_submit_wps64x('

    options validvarname=any;
    libname sd1 "d:/sd1";
    data havGrp(keep=hisDay hisDte g valDayT valNiteT where=(not missing(g)));
      set sd1.have;
      seq=_n_;
      grp  = (_n_ - mod(seq-1,10) +9)/10+10;
      g   = grp            ;  valDayT = dayt; valNiteT= nitet; output;
      g   = lag(grp)  + 10 ;  valDayT = dayt; valNiteT= nitet; output;
      g  =  lag2(grp) + 20 ;  valDayT = dayt; valNiteT= nitet; output;
    run;quit;

    proc sql;
      create
         table sd1.want as
      select
         min(hisDte)   as dteMin   format=yymmdd10.
        ,avg(valDayT)  as avgDayT
        ,avg(valNiteT) as avgNiteT
      from
         havGrp
      group
         by g
      having
         count(g)=10
    ;quit;

    proc print data=sd1.want;
    run;quit;

    ');

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  The WPS System                                                                                                        */
    /*                       AVG     AVG                                                                                      */
    /*  Obs        dteMin    DayT    NiteT                                                                                    */
    /*                                                                                                                        */
    /*   1     1978-12-31    20.8    19.6                                                                                     */
    /*   2     1979-01-10    25.7    16.5                                                                                     */
    /*   3     1979-01-20    21.8    21.8                                                                                     */
    /*   4     1979-01-01    20.6    19.3                                                                                     */
    /*   5     1979-01-11    27.1    16.9                                                                                     */
    /*   6     1979-01-21    22.0    21.9                                                                                     */
    /*   7     1979-01-02    19.2    20.4                                                                                     */
    /*   8     1979-01-12    27.7    15.6                                                                                     */
    /*   9     1979-01-22    21.5    22.8                                                                                     */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                                                   _     _                _
    |___ \  __      ___ __  ___   _ __   ___ ___  _ __ ___ | |__ (_)_ __   ___  __| |
      __) | \ \ /\ / / `_ \/ __| | `__| / __/ _ \| `_ ` _ \| `_ \| | `_ \ / _ \/ _` |
     / __/   \ V  V /| |_) \__ \ | |   | (_| (_) | | | | | | |_) | | | | |  __/ (_| |
    |_____|   \_/\_/ | .__/|___/ |_|    \___\___/|_| |_| |_|_.__/|_|_| |_|\___|\__,_|
                     |_|
    */

    proc datasets lib=sd1 nolist nodetails;delete want havgrp; run;quit;

    %utl_submit_wps64x('

    options validvarname=any
    ;
    libname sd1 "d:/sd1";

    /*---- This creates the group variable G                                 ----*/
    data sd1.havGrp(keep=hisDay hisDt g valDayT valNiteT where=(not missing(g)));
      set sd1.have;
      hisdt = put(hisdte, yymmdd10.);
      seq=_n_;
      grp  = (_n_ - mod(seq-1,10) +9)/10+10;
      g   = grp            ;  valDayT = dayt; valNiteT= nitet; output;
      g   = lag(grp)  + 10 ;  valDayT = dayt; valNiteT= nitet; output;
      g  =  lag2(grp) + 20 ;  valDayT = dayt; valNiteT= nitet; output;
    run;quit ;

    proc r;
    export data=sd1.havGrp r=havGrp;
    submit;
    library(sqldf);
      want <-sqldf("
         select
            min(hisDt)    as dteMin
           ,avg(valDayT)  as avgDayT
           ,avg(valNiteT) as avgNiteT
         from
            havGrp
         where
            g <> 0
         group
            by g
         having
            count(g)=10
      ");
    want;
    endsubmit;
    import data=sd1.want r=want;
    run;quit;
    ');

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* The WPS System                                                                                                         */
    /*                                                                                                                        */
    /*       dteMin avgDayT avgNiteT                                                                                          */
    /* 1 1978-12-31    20.8     19.6                                                                                          */
    /* 2 1979-01-10    25.7     16.5                                                                                          */
    /* 3 1979-01-20    21.8     21.8                                                                                          */
    /* 4 1979-01-01    20.6     19.3                                                                                          */
    /* 5 1979-01-11    27.1     16.9                                                                                          */
    /* 6 1979-01-21    22.0     21.9                                                                                          */
    /* 7 1979-01-02    19.2     20.4                                                                                          */
    /* 8 1979-01-12    27.7     15.6                                                                                          */
    /* 9 1979-01-22    21.5     22.8                                                                                          */
    /*                                                                                                                        */
    /* BACK to PARENT                                                                                                         */
    /*                                                                                                                        */
    /* Obs      DTEMIN      AVGDAYT    AVGNITET                                                                               */
    /*                                                                                                                        */
    /*  1     1978-12-31      20.8       19.6                                                                                 */
    /*  2     1979-01-10      25.7       16.5                                                                                 */
    /*  3     1979-01-20      21.8       21.8                                                                                 */
    /*  4     1979-01-01      20.6       19.3                                                                                 */
    /*  5     1979-01-11      27.1       16.9                                                                                 */
    /*  6     1979-01-21      22.0       21.9                                                                                 */
    /*  7     1979-01-02      19.2       20.4                                                                                 */
    /*  8     1979-01-12      27.7       15.6                                                                                 */
    /*  9     1979-01-22      21.5       22.8                                                                                 */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                                _   _                                      _     _                _
    __      ___ __  ___   _ __  _   _| |_| |__   ___  _ __    ___ ___  _ __ ___ | |__ (_)_ __   ___  __| |
    \ \ /\ / / `_ \/ __| | `_ \| | | | __| `_ \ / _ \| `_ \  / __/ _ \| `_ ` _ \| `_ \| | `_ \ / _ \/ _` |
     \ V  V /| |_) \__ \ | |_) | |_| | |_| | | | (_) | | | || (_| (_) | | | | | | |_) | | | | |  __/ (_| |
      \_/\_/ | .__/|___/ | .__/ \__, |\__|_| |_|\___/|_| |_| \___\___/|_| |_| |_|_.__/|_|_| |_|\___|\__,_|
             |_|         |_|    |___/

    */

    proc datasets lib=sd1 nolist nodetails;delete want havgrp; run;quit;

    %utl_submit_wps64x('

    libname sd1 "d:/sd1";
    options validvarname=any;

    data sd1.havGrp(keep=hisDay hisDt g valDayT valNiteT where=(not missing(g)));
      set sd1.have;
      hisdt = put(hisdte, yymmdd10.);
      seq=_n_;
      grp  = (_n_ - mod(seq-1,10) +9)/10+10;
      g   = grp            ;  valDayT = dayt; valNiteT= nitet; output;
      g   = lag(grp)  + 10 ;  valDayT = dayt; valNiteT= nitet; output;
      g  =  lag2(grp) + 20 ;  valDayT = dayt; valNiteT= nitet; output;
    run;quit ;

    proc python;
    export data=sd1.havgrp python=havgrp;
    submit;
    print(havgrp);
    from os import path;
    import pandas as pd;
    import numpy as np;
    import pandas as pd;
    from pandasql import sqldf;
    mysql = lambda q: sqldf(q, globals());
    from pandasql import PandaSQL;
    pdsql = PandaSQL(persist=True);
    sqlite3conn = next(pdsql.conn.gen).connection.connection;
    sqlite3conn.enable_load_extension(True);
    sqlite3conn.load_extension("c:/temp/libsqlitefunctions.dll");
    mysql = lambda q: sqldf(q, globals());
    want = pdsql("""
         select
            min(hisDt)    as dteMin
           ,avg(valDayT)  as avgDayT
           ,avg(valNiteT) as avgNiteT
         from
            havgrp
         where
            g <> 0
         group
            by g
         having
            count(g)=10
      """);
    print(want);
    endsubmit;
    import data=sd1.want python=want;
    run;quit;
    ');

    proc print data=sd1.want;
    run;quit;

     /**************************************************************************************************************************/
     /*                                                                                                                        */
     /* The WPS System                                                                                                         */
     /*                                                                                                                        */
     /* The PYTHON Procedure                                                                                                   */
     /*                                                                                                                        */
     /*                                                                                                                        */
     /* [93 rows x 5 columns]                                                                                                  */
     /*                                                                                                                        */
     /*        dteMin  avgDayT  avgNiteT                                                                                       */
     /* 0  1978-12-31     20.8      19.6                                                                                       */
     /* 1  1979-01-10     25.7      16.5                                                                                       */
     /* 2  1979-01-20     21.8      21.8                                                                                       */
     /* 3  1979-01-01     20.6      19.3                                                                                       */
     /* 4  1979-01-11     27.1      16.9                                                                                       */
     /* 5  1979-01-21     22.0      21.9                                                                                       */
     /* 6  1979-01-02     19.2      20.4                                                                                       */
     /* 7  1979-01-12     27.7      15.6                                                                                       */
     /* 8  1979-01-22     21.5      22.8                                                                                       */
     /*                                                                                                                        */
     /* WPS                                                                                                                    */
     /*                                                                                                                        */
     /*                       avg     avg                                                                                      */
     /* Obs      dteMin      DayT    NiteT                                                                                     */
     /*                                                                                                                        */
     /*  1     1978-12-31    20.8     19.6                                                                                     */
     /*  2     1979-01-10    25.7     16.5                                                                                     */
     /*  3     1979-01-20    21.8     21.8                                                                                     */
     /*  4     1979-01-01    20.6     19.3                                                                                     */
     /*  5     1979-01-11    27.1     16.9                                                                                     */
     /*  6     1979-01-21    22.0     21.9                                                                                     */
     /*  7     1979-01-02    19.2     20.4                                                                                     */
     /*  8     1979-01-12    27.7     15.6                                                                                     */
     /*  9     1979-01-22    21.5     22.8                                                                                     */
     /*                                                                                                                        */
     /*                                                                                                                        */
     /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */

    /*        _       _           _
     _ __ ___| | __ _| |_ ___  __| |  _ __ ___ _ __   ___  ___
    | `__/ _ \ |/ _` | __/ _ \/ _` | | `__/ _ \ `_ \ / _ \/ __|
    | | |  __/ | (_| | ||  __/ (_| | | | |  __/ |_) | (_) \__ \
    |_|  \___|_|\__,_|\__\___|\__,_| |_|  \___| .__/ \___/|___/
                                              |_|
    */
    https://github.com/rogerjdeangelis/utl-calculating-a-weighted-or-moving-sum-for-a-window-of-size-three
    https://github.com/rogerjdeangelis/utl-forecast-the-next-four-months-using-a-moving-average-time-series
    https://github.com/rogerjdeangelis/utl-forecast-the-next-seven-days-using-a--moving-average-model-in-R
    https://github.com/rogerjdeangelis/utl-moving-ten-month-average-by-group
    https://github.com/rogerjdeangelis/utl-using-Barts-dynamic-arrays-to-collapse-columns-by-removing-missing-values
    https://github.com/rogerjdeangelis/utl-weight-loss-over-thirty-day-rolling-moving-windows-using-weekly-values
    https://github.com/rogerjdeangelis/utl-weighted-moving-sum-for-several-variables
    https://github.com/rogerjdeangelis/utl_R_moving_average_six_variables_by_group
    https://github.com/rogerjdeangelis/utl_moving_average_of_centered_timeseries_or_calculate_a_modified_version_of_moving_averages
    https://github.com/rogerjdeangelis/utl-Rolling-four-month-median-by-group
    https://github.com/rogerjdeangelis/utl-betas-for-rolling-regressions
    https://github.com/rogerjdeangelis/utl-calculating-three-year-rolling-moving-weekly-and-annual-daily-standard-deviation
    https://github.com/rogerjdeangelis/utl-compute-the-partial-and-total-rolling-sums-for-window-of-size-of-three
    https://github.com/rogerjdeangelis/utl-controlling-the-order-of-transposed-variables-using-interleave-set
    https://github.com/rogerjdeangelis/utl-creating-rolling-sets-of-monthly-tables
    https://github.com/rogerjdeangelis/utl-how-to-compare-price-observations-in-rolling-time-intervals
    https://github.com/rogerjdeangelis/utl-parallell-processing-a-rolling-moving-three-month-ninety-day-skewness-for-five-thousand-variable
    https://github.com/rogerjdeangelis/utl-rolling-moving-sum-and-count-over-3-day-window-by-id
    https://github.com/rogerjdeangelis/utl-rolling-sum_of-six-months-by-group
    https://github.com/rogerjdeangelis/utl-timeseries-rolling-three-day-averages-by-county
    https://github.com/rogerjdeangelis/utl-weight-loss-over-thirty-day-rolling-moving-windows-using-weekly-values
    https://github.com/rogerjdeangelis/utl_calculate-moving-rolling-average-with-gaps-in-years
    https://github.com/rogerjdeangelis/utl_calculating_rolling_3_month_skewness_of_prices_by_stock
    https://github.com/rogerjdeangelis/utl_calculating_the_rolling_product_using_wps_sas_and_r
    https://github.com/rogerjdeangelis/utl_comparison_sas_v_r_increment_a_rolling_sum_with_the_first_value_for_each_id
    https://github.com/rogerjdeangelis/utl_count_distinct_ids_in_rolling_overlapping_date_ranges
    https://github.com/rogerjdeangelis/utl_excluding_rolling_regressions_with_one_on_more_missing_values_in_the_window
    https://github.com/rogerjdeangelis/utl_nice_hash_example_of_rolling_count_of_dates_plus-minus_2_days_of_current_date
    https://github.com/rogerjdeangelis/utl_rolling_means_by-quarter_semiannual_and_yearly
    https://github.com/rogerjdeangelis/utl_standard_deviation_of_90_day_rolling_standard_deviations


    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
Tumbling goups of ten and n temperatures similar eolling and moving means wps r python 
