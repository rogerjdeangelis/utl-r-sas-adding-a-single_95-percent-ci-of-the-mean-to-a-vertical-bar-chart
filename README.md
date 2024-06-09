# utl-r-sas-adding-a-single_95-percent-ci-of-the-mean-to-a-vertical-bar-chart
R sas adding a single_95 percent ci of the mean to a vertical bar chart 
    %let pgm=utl-r-sas-adding-a-single_95-percent-ci-of-the-mean-to-a-vertical-bar-chart;

    R sas adding a single_95 percent ci of the mean to a vertical bar chart

          Two Solutions
             1 SAS
             2 R

    Hi Res graph witout annotation
    https://tinyurl.com/y92jb57j
    https://github.com/rogerjdeangelis/utl-r-sas-adding-a-single_95-percent-ci-of-the-mean-to-a-vertical-bar-chart/blob/main/bar.png

    github
    https://tinyurl.com/292x5am7
    https://github.com/rogerjdeangelis/utl-r-sas-adding-a-single_95-percent-ci-of-the-mean-to-a-vertical-bar-chart

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    This solution only applies to equal size groups and large sample size normal limits,

    /************************************************************************************************************************************************/
    /*  _                           |                                       |                                                                       */
    /* / |  ___  __ _ ___           |                                       |                                                                       */
    /* | | / __|/ _` / __|          |                                       |                                                                       */
    /* | | \__ \ (_| \__ \          |                                       |                                                                       */
    /* |_| |___/\__,_|___/          |                                       |                                                                       */
    /*                              |                                       |                                                                       */
    /* SD1.HAVE total obs=30        | proc sql;                             |               GRP1      GRP2      GRP3      GRP4      GRP5            */
    /*                              |  select                               |        --------------------------------------------------------       */
    /*   GRP        VAL             |   avg(val)   as mean                  |        |                                                      |       */
    /*                              |   ,std(val)  as stddev                |        |95% CI on the Standard Error of the mean              |       */
    /*   grp1     0.77355           |   ,std(val)                           |        |                                                      |       */
    /*   grp1    -4.24625           |      /sqrt(count(val)) as stderr      |      5 +                                   4.66               +  5    */
    /*   grp1     0.93005           |   ,avg(val) + 1.96*std(val)           |        |           Statistics              -----              |       */
    /*   grp1    -2.09927           |      /sqrt(count(val)) as upr         |        |                                   #####              |       */
    /*   grp1    -5.69762           |   ,avg(val) - 1.96*std(val)           |        |    mean   stddev   upr     lwr    #####              |       */
    /*   grp1     5.64881           |      /sqrt(count(val)) as lwr         |      4 + --------------------------------  #####              +  4    */
    /*   ...                        |  into                                 |        | 0.53267  3.6954   1.8550 -0.7897  #####              |       */
    /*   grp5     1.24119           |    :mean                              |        |                                   #####              |       */
    /*   grp5     7.12028           |   ,:stddev                            |        |  Manual calculation of 95% CI     #####              |       */
    /*   grp5    -0.27753           |   ,:stderr                            |      3 +                                   #####              |+  3   */
    /*   grp5     3.29268           |   ,:upr                               |        |  upr = 0.53+1.96*3.70/sqrt(30)    #####              |       */
    /*   grp5     0.33434           |   ,:lwr                               |  GROUP |  lwr = 0.53+1.96*3.70/sqrt(30)    #####              | GROUP */
    /*   grp5    -1.70212           |  from                                 |  MEANS |                                   #####              | MEANS */
    /*                              |   sd1.have                            |      2 +                                   #####     1.67     +  2    */
    /*                              | ;quit;                                |        |<--- Upper 1.86 -------------------#####-----#####--->|       */
    /* libname sd1 "d:/sd1";        |                                       |        |                               ^   #####     #####    |       */
    /* options validvarname=upcase; | options ls=64 ps=36;                  |        |                               |   #####     #####    |       */
    /* data sd1.have;               | proc chart data=sd1.have;             |      1 +            95% CI(1.86,-.79)  |   #####     #####    +  1    */
    /*   call streaminit(1274961);  |  vbar grp /                           |        |                               |   #####     #####    |       */
    /*   do i=1 to 5;               |       sumvar=val                      |        |                               |   #####     #####    |       */
    /*      grp=cats('grp',i);      |       type=mean                       |        |                               |   #####     #####    |       */
    /*      do gens=1 to 6;         |       ref=&upr &lwr                   |        +<--- Overall Mean = 0.53 ------|--------------------->+       */
    /*         val=rand(            |       gspace=1;                       |      0 +                               |                      |+ 0    */
    /*          'normal',i-3,3.5);  | run;quit;                             |        |     #####     #####     ##### |                      |       */
    /*        output;               |                                       |        |     #####     #####     ##### V                      |       */
    /*      end;                    |                                       |        |<----#####-----#####-----#####----- Lower = = -.79 -->|       */
    /*    end;                      |                                       |     -1 +     -0.78     #####     #####                        + -1    */
    /*    drop i gens;              |                                       |        |               #####     #####                        |       */
    /*    stop;                     |                                       |        |               -1.20     #####                        |       */
    /* run;quit;                    |                                       |        |                         #####                        |       */
    /*                              |                                       |     -2 |                         -1,68                        |       */
    /*                              |                                       |        -------------------------------------------------------- -2    */
    /*                              |                                       |               GRP1      GRP2      GRP3      GRP4      GRP5            */
    /*                              |                                       |                                                                       */
    /*                              |                                       | Because of equal sized groups the overall mean = mean of the 5 groups */                                       *
    /*                              |                                       |                                                                       */
    /*                              |                                       | grandMean= (-0.781-1.203-1.682+4.662+1.668)/5 =  0.5328               */
    /*                              |                                       |                                                                       */
    /*------------------------------+---------------------------------------+-----------------------------------------------------------------------*/
    /*  ___     ____                |                                       |                                                                       */
    /* |___ \  |  _ \               |                                       |                                                                       */
    /*   __) | | |_) |              |                                       |                                                                       */
    /*  / __/  |  _ <               |                                       |                                                                       */
    /* |_____| |_| \_\              |                                       |                                                                       */
    /*                              |                                       |                                                                       */
    /*    same input                |                                       |                                                                       */
    /*                              | %utlfkil(d:/png/bar.png);             |    Hi Res Graph                                                       */
    /*                              |                                       |    https://tinyurl.com/y92jb57j                                       */
    /*                              | %utl_rbegin;                          |                                                                       */
    /*                              | parmcards4;                           |                                                                       */
    /*                              |  library(ggplot2)                     |                                                                       */
    /*                              |  library(haven);                      |                                                                       */
    /*                              |  library(sqldf);                      |                                                                       */
    /*                              |  have<-read_sas(                      |                                                                       */
    /*                              |    "d:/sd1/have.sas7bdat")            |                                                                       */
    /*                              |  grpavg<-sqldf('                      |                                                                       */
    /*                              |    select                             |                                                                       */
    /*                              |       GRP                             |                                                                       */
    /*                              |      ,avg(VAL) as avgval              |                                                                       */
    /*                              |    from                               |                                                                       */
    /*                              |       have                            |                                                                       */
    /*                              |    group                              |                                                                       */
    /*                              |       by grp                          |                                                                       */
    /*                              |  ')                                   |                                                                       */
    /*                              |  grpavg                               |                                                                       */
    /*                              |  overall_mean <-                      |                                                                       */
    /*                              |    mean(have$VAL, na.rm = TRUE)       |                                                                       */
    /*                              |  overall_sd <-                        |                                                                       */
    /*                              |    sd(have$VAL, na.rm = TRUE)         |                                                                       */
    /*                              |  overall_sd                           |                                                                       */
    /*                              |  overall_se <-                        |                                                                       */
    /*                              |   overall_sd/sqrt(length(have$VAL))   |                                                                       */
    /*                              |  overall_se                           |                                                                       */
    /*                              |  overall_mean                         |                                                                       */
    /*                              |  ci_95 <- overall_mean +              |                                                                       */
    /*                              |   c(-1, 1) * 1.96 * overall_se;       |                                                                       */
    /*                              |  ci_95                                |                                                                       */
    /*                              |  png("d:/png/bar.png")                |                                                                       */
    /*                              |  ggplot(grpavg, aes(                  |                                                                       */
    /*                              |      x = GRP                          |                                                                       */
    /*                              |    , y = avgval, fill="pink")) +      |                                                                       */
    /*                              |    geom_bar(stat = "identity"         |                                                                       */
    /*                              |    , position = "dodge") +            |                                                                       */
    /*                              |    ylim(-2, 6) +                      |                                                                       */
    /*                              |    theme(legend.position = "none")  + |                                                                       */
    /*                              |    geom_hline(                        |                                                                       */
    /*                              |    yintercept = ci_95[1]              |                                                                       */
    /*                              |    , linetype = "solid"               |                                                                       */
    /*                              |    , color = "black"                  |                                                                       */
    /*                              |    ,linewidth =1.25) +                |                                                                       */
    /*                              |    geom_hline(                        |                                                                       */
    /*                              |    yintercept = ci_95[2]              |                                                                       */
    /*                              |   , linetype = "solid"                |                                                                       */
    /*                              |   , color = "black"                   |                                                                       */
    /*                              |   ,linewidth =1.25)                   |                                                                       */
    /*                              |  png()                                |                                                                       */
    /*                              | ;;;;                                  |                                                                       */
    /*                              | %utl_rend;                            |                                                                       */
    /*                              |                                       |                                                                       */
    /************************************************************************************************************************************************/


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
      call streaminit(1274961);
      do i=1 to 5;
         grp=cats('grp',i);
         do gens=1 to 6;
            val=rand(
             'normal',i-3,3.5);
           output;
         end;
       end;
       drop i gens;
       stop;
    run;quit;

    /*****************************************************************************************************************************/
    /*                                                                                                                           */
    /* SD1.HAVE total obs=30                                                                                                     */
    /*                                                                                                                           */
    /*   GRP        VAL                                                                                                          */
    /*                                                                                                                           */
    /*   grp1     0.77355                                                                                                        */
    /*   grp1    -4.24625                                                                                                        */
    /*   grp1     0.93005                                                                                                        */
    /*   grp1    -2.09927                                                                                                        */
    /*   grp1    -5.69762                                                                                                        */
    /*   grp1     5.64881                                                                                                        */
    /*   ...                                                                                                                     */
    /*   grp5     1.24119                                                                                                        */
    /*   grp5     7.12028                                                                                                        */
    /*   grp5    -0.27753                                                                                                        */
    /*   grp5     3.29268                                                                                                        */
    /*   grp5     0.33434                                                                                                        */
    /*   grp5    -1.70212                                                                                                        */
    /*                                                                                                                           */
    /*****************************************************************************************************************************/

    /*
    / |  ___  __ _ ___   _ __  _ __ ___   ___ ___  ___ ___
    | | / __|/ _` / __| | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | | \__ \ (_| \__ \ | |_) | | | (_) | (_|  __/\__ \__ \
    |_| |___/\__,_|___/ | .__/|_|  \___/ \___\___||___/___/
                        |_|
    */

    proc sql;
     select
      avg(val)   as mean
      ,std(val)  as stddev
      ,std(val)
         /sqrt(count(val)) as stderr
      ,avg(val) + 1.96*std(val)
         /sqrt(count(val)) as upr
      ,avg(val) - 1.96*std(val)
         /sqrt(count(val)) as lwr
     into
       :mean
      ,:stddev
      ,:stderr
      ,:upr
      ,:lwr
     from
      sd1.have
    ;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  Manual calculate large sample 95% CI                                                                                  */
    /*                                                                                                                        */
    /*      mean    stddev    stderr       upr       lwr                                                                      */
    /*  ------------------------------------------------                                                                      */
    /*  0.532668  3.695403  0.674685  1.855051  -0.78972                                                                      */
    /*                                                                                                                        */
    /*  Manual calculation large sample 95% CI                                                                                */
    /*                                                                                                                        */
    /*  0.532668 + 1.96 * 3.695403/sqrt(30)   1.8550509804 upr                                                                */
    /*  0.532668 - 1.96 * 3.695403/sqrt(30)   -0.78971498  lwr                                                                */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    options ls=64 ps=36;
    proc chart data=sd1.have;
     vbar grp /
          sumvar=val
          type=mean
          ref=&upr &lwr
          gspace=1;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*              GRP1      GRP2      GRP3      GRP4      GRP5                                                              */
    /*       --------------------------------------------------------                                                         */
    /*       |                                                      |                                                         */
    /*       |95% CI on the Standard Error of the mean              |                                                         */
    /*       |                                                      |                                                         */
    /*     5 +                                   4.66               +  5                                                      */
    /*       |         Statistics                -----              |                                                         */
    /*       |                                   #####              |                                                         */
    /*       |    mean   stddev   upr     lwr    #####              |                                                         */
    /*     4 + --------------------------------  #####              +  4                                                      */
    /*       | 0.53267  3.6954   1.8550 -0.7897  #####              |                                                         */
    /*       |                                   #####              |                                                         */
    /*       |                                   #####              |                                                         */
    /*     3 +                                   #####              |+  3                                                     */
    /*       |                                   #####              |                                                         */
    /* GROUP |                                   #####              | GROUP                                                   */
    /* MEANS |                                   #####              | MEANS                                                   */
    /*     2 +                                   #####     1.67     +  2                                                      */
    /*       |<--- Upper 1.86 -------------------#####-----#####--->|                                                         */
    /*       |                               ^   #####     #####    |                                                         */
    /*       |                               |   #####     #####    |                                                         */
    /*     1 +            95% CI(1.86,-.79)  |   #####     #####    +  1                                                      */
    /*       |                               |   #####     #####    |                                                         */
    /*       |                               |   #####     #####    |                                                         */
    /*       |                               |   #####     #####    |                                                         */
    /*       +<--- Overall Mean = 0.53 ------|--------------------->+                                                         */
    /*     0 +                               |                      |+ 0                                                      */
    /*       |     #####     #####     ##### |                      |                                                         */
    /*       |     #####     #####     ##### V                      |                                                         */
    /*       |<----#####-----#####-----#####----- Lower = = -.79 -->|                                                         */
    /*    -1 +     -0.78     #####     #####                        + -1                                                      */
    /*       |               #####     #####                        |                                                         */
    /*       |               -1.20     #####                        |                                                         */
    /*       |                         #####                        |                                                         */
    /*    -2 |                         -1,68                        |                                                         */
    /*       -------------------------------------------------------- -2                                                      */
    /*              GRP1      GRP2      GRP3      GRP4      GRP5                                                              */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___    ____
    |___ \  |  _ \
      __) | | |_) |
     / __/  |  _ <
    |_____| |_| \_\

    */


    %utlfkil(d:/png/bar.png);

    %utl_rbegin;
    parmcards4;
     library(ggplot2)
     library(haven);
     library(sqldf);
     have<-read_sas(
       "d:/sd1/have.sas7bdat")
     grpavg<-sqldf('
       select
          GRP
         ,avg(VAL) as avgval
       from
          have
       group
          by grp
     ')
     grpavg
     overall_mean <-
       mean(have$VAL, na.rm = TRUE)
     overall_sd <-
       sd(have$VAL, na.rm = TRUE)
     overall_sd
     overall_se <-
      overall_sd/sqrt(length(have$VAL))
     overall_se
     overall_mean
     ci_95 <- overall_mean +
      c(-1, 1) * 1.96 * overall_se;
     ci_95
     png("d:/png/bar.png")
     ggplot(grpavg, aes(
         x = GRP
       , y = avgval, fill="pink")) +
       geom_bar(stat = "identity"
       , position = "dodge") +
       ylim(-2, 6) +
       theme(legend.position = "none")  +
       geom_hline(
       yintercept = ci_95[1]
       , linetype = "solid"
       , color = "black"
       ,linewidth =1.25) +
       geom_hline(
       yintercept = ci_95[2]
      , linetype = "solid"
      , color = "black"
      ,linewidth =1.25)
     png()
    ;;;;
    %utl_rend;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  Hi Res Graph                                                                                                          */
    /*  https://tinyurl.com/y92jb57j                                                                                          */
    /*                                                                                                                        */
    /*  >  overall_mean                                                                                                       */
    /*  [1] 0.5326677                                                                                                         */
    /*                                                                                                                        */
    /*  >  overall_sd                                                                                                         */
    /*  [1] 3.695403                                                                                                          */
    /*                                                                                                                        */
    /*  >  ci_95                                                                                                              */
    /*  [1] -0.7897152  1.8550506                                                                                             */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
