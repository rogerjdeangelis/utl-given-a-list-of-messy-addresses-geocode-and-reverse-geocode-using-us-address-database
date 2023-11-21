# utl-given-a-list-of-messy-addresses-geocode-and-reverse-geocode-using-us-address-database
Given a list of messy addresses geocode and reverse geocode using downloadable us address database
    %let pgm=utl-given-a-list-of-messy-addresses-geocode-and-reverse-geocode-using-us-address-database;

    Given a list of messy addresses geocode and reverse geocode using downloadable us address database;

    No api, no restrictions, no credit card, and internet once just for download of us addresses


    %stop_submission;  * in case you accidentally submit the whole thing at once *;

    The 141 million addresses are  tiny 1gb in a self extracting zip file(7zip ultra).

    Only need two inputs, your list and the 141 million(all have zipcode).

    ORIGINAL SOURCES

       https://www.transportation.gov/mission/open/gis/national-address-database/national-address-database-nad-disclaimer
       https://batch.openaddresses.io/data#map=0.45/-23.2/46.3
       https://bcdcspatial.blogspot.com/2012/09/normalize-to-usps-street-abbreviations.html

    PROCESS

       Because the download is an 7zip self extracting executable you may want to use an incognito window or
       allow my 'not' unsafe to download. It is safe to download.

          1. download unzip and convert csv to table the 141 million addresses (1.07gb win10 1,159,132,788 bytes)
                https://1drv.ms/u/s!AovFHZtMPA-7gh25NUe6pg0OW68k?e=kfZLOe

          2. index using zip4 on the  141 million addresses

          3. load sample addresses (not from 141 million)

          4. Clean/Standardize sample addresses

          5. Match sample addresses to 141 million and get geocodes (latitude and longitudes)


    The process might be slow so you may want to use SPDE with a parrallel index and maybe 16 simutaneos tasks.
    Note I have 128gb ram and 32 logical processors and 2 1tb NVMe drives.
    There is one bad record in the CSV, Will fix in the future  _N_=62038426;


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  87% Match  We have geocoded the addresses    |                                                                        */
    /*                                               |                                                                        */
    /*                                               |                                                                        */
    /*                 INPUT                         |              OUTPUT  (0 is perfect match )                             */
    /*  ==========================================   |  =============================================================         */
    /*                                               |                                                          MIN_          */
    /*  Obs    BST_STREET                 BST_ZIP5   |    TOO_ADR                         AVGLON     AVGLAT     SCORE         */
    /*                                               |                                                                        */
    /*    1    1 RANDALL SQ 02904            05205   |    1 RANDALL SQ 02904             41.8368    -71.4106        0         */
    /*    2    100 CROSSWYNDS DR 02874       02542   |    100 CROSSWYNDS DR 02874        41.4979    -71.4237        0         */
    /*    3    100 HOSPITAL DR 05201         02902   |    100 HOSPITAL DR 05201          42.8741    -73.2075        0         */
    /*    4    100 TER HEUN DR 02540         01821   |    100 TER HEUN DR 02540          41.5642    -70.6224        0         */
    /*    5    1076 N MAIN ST 02904          06526   |    1076 N MAIN ST 02904           41.8504    -71.4037        0         */
    /*    6    11 DAKOTA DR 01824            05405   |    11 DAKOTA DR 01824             42.5714    -71.3579        0         */
    /*    7    11 LUNAR DR 06525             06896   |    11 LUNAR DR 06525              41.3467    -72.9765        0         */
    /*    8    111 COLCHESTER AVE 05401      02902   |    111 COLCHESTER AVE 05401       44.4798    -73.1940        0         */
    /*    9    112 PORTLAND AVE 06896        04104   |    112 PORTLAND AVE 06896         41.2632    -73.4347        0         */
    /*   10    117 ELLENFIELD ST 02905       02062   |    117 ELLENFIELD ST 02905        41.7939    -71.4013        0         */
    /*   11    119 GANNETT DR 04106          02162   |    119 GANNETT DR 04106           43.6370    -70.3550        0         */
    /*   12    125 CENTRAL ST 02062          07007   |    125 CENTRAL ST 02062           42.1941    -71.1997        0         */
    /*   13    1250 HANCOCK ST 02169         07037   |    1250 HANCOCK ST 02169          42.2524    -71.0030        0         */
    /*   14    130 POWERVILLE RD 07005       06706   |    130 POWERVILLE RD 07005        40.9093    -74.4254        0         */
    /*   15    1333 HUDSON ST 07030          01101   |    1333 HUDSON ST 07030           40.7528    -74.0247        0         */
    /*   16    140 GRANDVIEW AVE 06708       01771   |    140 GRANDVIEW AVE 06708        41.5607    -73.0606        0         */
    /*   17    140 HIGH ST 01105             06516   |    140 HIGH ST 01105              42.1053    -72.5796        0         */
    /*   18    144 N RD 01776                06496   |    144 N RD 01776                 42.4204    -71.3928        0         */
    /*   19    1450 CHAPEL ST 06511          01201   |    1450 CHAPEL ST 06511           41.3105    -72.9436        0         */
    /*   20    1460 TUTTLE AVE 06492         01451   |    1460 TUTTLE AVE 06492          41.4643    -72.8687        0         */
    /*   21    15 BISHOP PKWY 01201          04264   |    15 BISHOP PKWY 01201           42.4389    -73.2406        0         */
    /*   22    165 ML ST 01453               02142   |    165 ML ST 01453                42.5320    -71.7475        0         */
    /*   23    17 PARIS ST 04268             02152   |    17 PARIS ST 04268              44.2114    -70.5331        0         */
    /*   24    173 CHELSEA ST 02149          06516   |    173 CHELSEA ST 02149           42.4040    -71.0487        0         */
    /*   25    18 WOBURN ST 02155            06516   |    18 WOBURN ST 02155             42.4225    -71.1238        0         */
    /*   26    1844 WHITNEY AVE 06517        04104   |    1844 WHITNEY AVE 06517         41.3662    -72.9085        0         */
    /*   27    20 YORK ST 06510              07017   |    20 YORK ST 06510               41.3042    -72.9354        0         */
    /*   28    22 BRAMHALL ST 04102          06516   |    22 BRAMHALL ST 04102           43.6525    -70.2755        0         */
    /*   29    234 CLIFTON AVE 07011         06606   |    234 CLIFTON AVE 07011          40.8764    -74.1350        0         */
    /*   30    2408 WHITNEY AVE 06518        04074   |    2408 WHITNEY AVE 06518         41.3851    -72.9022        0         */
    /*   31    2409 MAIN ST 06606            02572   |    2409 MAIN ST 06606             41.1956    -73.1998        0         */
    /*   32    25 JUNE ST 04073              01601   |    25 JUNE ST 04073               43.4484    -70.7671        0         */
    /*   33    2527 CRANBERRY HWY 02571      04404   |    2527 CRANBERRY HWY 02571       41.7690    -70.7326        0         */
    /*   34    27 MECHANIC ST 01608          06516   |    27 MECHANIC ST 01608           42.2636    -71.8008        0         */
    /*   35    277 STATE ST 04401            06106   |    277 STATE ST 04401             44.8060    -68.7588        0         */
    /*   36    291 WHITNEY AVE 06511         02112   |    291 WHITNEY AVE 06511          41.3184    -72.9192        0         */
    /*   37    30 JORDAN LN 06109            02472   |    30 JORDAN LN 06109             41.7246    -72.6665        0         */
    /*   38    300 LONGWOOD AVE 02115        04244   |    300 LONGWOOD AVE 02115         42.3372    -71.1054        0         */
    /*   39    311 ARSENAL ST 02472          06066   |    311 ARSENAL ST 02472           42.3635    -71.1670        0         */
    /*   40    33 MOLLISON WAY 04240         06826   |    33 MOLLISON WAY 04240          44.1234    -70.1967        0         */
    /*   41    35 TALCOTTVILLE RD 06066      04244   |    35 TALCOTTVILLE RD 06066       41.8273    -72.4985        0         */
    /*   42    36 OLD KINGS HWY S 06820      07027   |    36 OLD KINGS HWY S 06820       41.0747    -73.4706        0         */
    /*   43    364 MAIN ST 04240             01101   |    364 MAIN ST 04240              44.1032    -70.2128        0         */
    /*   44    400 HARRISON AVE 07029        02142   |    400 HARRISON AVE 07029         40.7468    -74.1561        0         */
    /*   45    401 CHESTNUT ST 01104         02182   |    401 CHESTNUT ST 01104          42.1128    -72.5962        0         */
    /*   46    405 PEARL ST 02148            02742   |    405 PEARL ST 02148             42.4161    -71.0783        0         */
    /*   47    41 MONTVALE AVE 02180         06496   |    41 MONTVALE AVE 02180          42.4826    -71.1038        0         */
    /*   48    500 FAUNCE COR RD 02747       06476   |    500 FAUNCE COR RD 02747        41.6688    -70.9923        0         */
    /*   49    51 S WHITTLESEY AVE 06492     02112   |    51 S WHITTLESEY AVE 06492      41.4539    -72.8211        0         */
    /*   50    52 WASHINGTON AVE 06473       02422   |    52 WASHINGTON AVE 06473        41.3914    -72.8583        0         */
    /*   51    55 FRUIT ST 02114             01041   |    55 FRUIT ST 02114              42.3629    -71.0694        0         */
    /*   52    57 BEDFORD ST 02420           02902   |    57 BEDFORD ST 02420            42.4539    -71.2325        0         */
    /*   53    575 BEECH ST 01040            06336   |    575 BEECH ST 01040             42.2004    -72.6276        0         */
    /*   54    593 EDDY ST 02903             05405   |    593 EDDY ST 02903              41.8118    -71.4099        0         */
    /*   55    6 FERRO DR 06333              01601   |    6 FERRO DR 06333               41.3492    -72.2359        0         */
    /*   56    6 SAN REMO DR 05403           01761   |    6 SAN REMO DR 05403            44.4622    -73.1797        0         */
    /*   57    630 PLANTATION ST 01605       02302   |    630 PLANTATION ST 01605        42.2922    -71.7598        0         */
    /*   58    67 UN ST 01760                02092   |    67 UN ST 01760                 42.2804    -71.3354        0         */
    /*   59    680 CTR ST 02302              02112   |    680 CTR ST 02302               42.0879    -70.9913        0         */
    /*   60    690 CANTON ST 02090           02132   |    690 CANTON ST 02090            42.2016    -71.1609        0         */
    /*   61    720 HARRISON AVE 02118        01201   |    720 HARRISON AVE 02118         42.3378    -71.0709        0         */
    /*   62    725 CONCORD AVE 02138         06856   |    725 CONCORD AVE 02138          42.3907    -71.1524        0         */
    /*   63    725 N ST 01201                04404   |    725 N ST 01201                 42.4594    -73.2494        0         */
    /*   64    761 MAIN AVE 06851            01731   |    761 MAIN AVE 06851             41.1598    -73.4211        0         */
    /*   65    766 STILLWATER AVE 04401      02842   |    766 STILLWATER AVE 04401       44.8353    -68.7499        0         */
    /*   66    8 OAK PARK DR 01730           02742   |    8 OAK PARK DR 01730            42.5168    -71.2490        0         */
    /*   67    8 ODONNELL RD 02842           02172   |    8 ODONNELL RD 02842            41.4876    -71.2752        0         */
    /*   68    81 HAWTHORN ST 02740          01151   |    81 HAWTHORN ST 02740           41.6299    -70.9333        0         */
    /*   69    810 MAIN ST 02176             02052   |    810 MAIN ST 02176              42.4622    -71.0624        0         */
    /*   70    819 WORCESTER ST 01151        04104   |    819 WORCESTER ST 01151         42.1563    -72.5164        0         */
    /*   71    81B MAIN ST 02053             02842   |    81B MAIN ST 02053              42.1510    -71.4110        0         */
    /*   72    9 PINEHURST LN 04105          06266   |    9 PINEHURST LN 04105           43.7402    -70.2658        0         */
    /*   73    939 W MAIN RD 02842           06106   |    939 W MAIN RD 02842            41.5278    -71.2956        0         */
    /*   74    945 MANSFIELD CITY RD 06268   04104   |    945 MANSFIELD CITY RD 06268    41.7744    -72.2635        0         */
    /*                                                                                                                        */
    /*   Why I use zip4 Index (note the misspellngs BERNIE BIRNIE and ELDRIDGE ELDREDGE)                                     */
    /*                                                                                                                        */
    /*   75    1290 SILAS DEANE HWY 2 06109  01811   |    1290 SILAS DEANE HWY 06109     41.6862    -72.6546      100         */
    /*   76    144 STATE ST 04101            06036   |    144 STATE ST 04102             43.6516    -70.2651      100         */
    /*   77    20 AMICI WAY 7 01810          06906   |    20 AMICI WAY 01810             42.6579    -71.1453      100         */
    /*   78    263 FARMINGTON AVE 06030      06516   |    263 FARMINGTON AVE 06032       41.7318    -72.7929      100         */
    /*   79    30 SHELBURNE RD 06904         01101   |    30 SHELBURNE RD 06902          41.0550    -73.5527      100         */
    /*   80    300 GEORGE ST 6 06511         06426   |    300 GEORGE ST 06511            41.3046    -72.9310      100         */
    /*   81    354 BERNIE AVE 01107          07017   |    354 BIRNIE AVE 01107           42.1232    -72.6119      100         */
    /*   82    37 SAYBROOK RD 06426          02092   |    30 SAYBROOK RD 06426           41.3492    -72.3989      100         */
    /*   83    385 TREMONT AVE 07018         02652   |    388 TREMONT AVE 07018          40.7545    -74.2339      100         */
    /*   84    400 BLUE HL DR 02090          07017   |    409 BLUE HL DR 02090           42.2123    -71.1520      100         */
    /*   85    48 ELDRIDGE PKWY 02653        01831   |    48 ELDREDGE PKWY 02653         41.7798    -69.9930      100         */
    /*   86    721 CLIFTON AVE 07013         06106   |    701 CLIFTON AVE 07013          40.8679    -74.1534      100         */
    /*                                               |                                                                        */
    /*   87    1 PKWY 01830                  03883   |                                     .           .         2000         */
    /*   88    99 E RIV DR 06108             01851   |                                     .           .         2600         */
    /*   89    2 RAEDER DR 03885             01191   |                                     .           .         2800         */
    /*   90    62 BROWN ST 01850             03243   |                                     .           .         2800         */
    /*   91    3400 A MAIN ST 01199          03113   |                                     .           .         3200         */
    /*   92    80 HIGHLAND ST 03246          03803   |                                     .           .         3400         */
    /*   93    601 RIVERWAY PL 03110         02112   |                                     .           .         3600         */
    /*   94    105 CORPORATE DR 03801        06516   |                                     .           .         3800         */
    /*   95    15 PARK MAN ST WACC 02114     02112   |                                     .           .         4000         */
    /*   96    23122 TOWN WALK DR 06518      07027   |                                     .           .         4000         */
    /*   97    55 FRUIT ST S501002 02114     02722   |                                     .           .         4200         */
    /*   98    6 FRANK E RODGERS BLVD 07029  02462   |                                     .           .         4600         */
    /*   99    1030 PRESIDENT AVE 110 02720  02722   |                                     .           .         4800         */
    /*  100    830 BOYLSTON STSTE 211 02467  02462   |                                     .           .         4800         */
    /*                                               |                                                                        */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_ ___
    | | `_ \| `_ \| | | | __/ __|
    | | | | | |_) | |_| | |_\__ \
    |_|_| |_| .__/ \__,_|\__|___/
            |_|
    */
    /*----                                                                   ----*/
    /*---- Sample Addresses                                                                  ----*/
    /*----                                                                   ----*/

    libname adr "m:/adr";

    data adr.adr_010smpAdr;
     informat bst_state $2. bst_zip5 $5. bst_street $64.;
     input bst_state bst_zip5 bst_street &;
    cards4;
    IA 52404 5614 WHEATLAND SW DR
    IA 51503 800 MERCY DR
    IA 51101 801 5TH ST
    IA 50309 1215 PLEASANT ST
    IA 52577 1229 C AVE E
    IA 52245 540 E JEFFERSON ST
    IA 52406 1026 A AVE NE
    IA 50317 3509 E 29TH ST
    IA 52807 1820 E 54TH ST STE B
    IL 61530 102 N MYERS
    IL 60025 1201 HARMS RD
    IL 61866 1421 PINE AVE
    IL 60657 1712 W BELMONT AVE
    IL 60523 202 BRIDLE PATH CIR
    IL 62025 235 A S MAIN
    IL 60625 2906 W WILSON AVE
    IL 60091 346 WILSHIRE EAST DR
    IL 62864 4230 LINCOLNSHIRE DR G
    IL 61016 515 STONE RIDGE LN
    IL 60527 6201 WILDWOOD LN
    IL 60068 738 N WASHINGTON AVE
    IL 62232 901 BEAR CREEK CT
    IL 60637 5841 S MARYLAND AVE
    IL 60611 680 N LAKE SHORE DR STE 1000
    IL 60153 2160 S 1ST AVE BLDG 101 STE 1752
    IL 60611 225 E CHICAGO AVE
    IL 60612 1900 W POLK ST
    IL 62703 1025 S 6TH ST
    IL 60612 1653 W CONGRESS BLVD
    IL 62781 701 N 1ST ST
    IL 60506 1870 W GALENA BLVD
    IL 60657 836 W WELLINGTON STE 7405
    IL 60099 2520 ELISHA AVE
    IL 60005 800 W CENTRAL RD
    IL 60426 71 W 156TH ST
    IL 61108 2550 CHARLES ST
    IL 60805 2800 W 95TH ST 3N
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  40 obs from last table WORK.ADR_010SMPADR total obs=39 21NOV2023:07:21:05                                             */
    /*                                                                                                                        */
    /*   BST_STATE    BST_ZIP5    BST_STREET                                                                                   */
    /*                                                                                                                        */
    /*      IA        52404      5614 WHEATLAND SW DR                                                                         */
    /*      IA        51503      800 MERCY DR                                                                                 */
    /*      IA        51101      801 5TH ST                                                                                   */
    /*      IA        50309      1215 PLEASANT ST                                                                             */
    /*      IA        52577      1229 C AVE E                                                                                 */
    /*      IA        52245      540 E JEFFERSON ST                                                                           */
    /*      IA        52406      1026 A AVE NE                                                                                */
    /*      IA        50317      3509 E 29TH ST                                                                               */
    /*      IA        52807      1820 E 54TH ST STE B                                                                         */
    /*      IL        61530      102 N MYERS                                                                                  */
    /*      IL        60025      1201 HARMS RD                                                                                */
    /*      IL        61866      1421 PINE AVE                                                                                */
    /*      IL        60657      1712 W BELMONT AVE                                                                           */
    /*      IL        60523      202 BRIDLE PATH CIR                                                                          */
    /*      IL        62025      235 A S MAIN                                                                                 */
    /*      IL        60625      2906 W WILSON AVE                                                                            */
    /*      IL        60091      346 WILSHIRE EAST DR                                                                         */
    /*      IL        62864      4230 LINCOLNSHIRE DR G                                                                       */
    /*      IL        61016      515 STONE RIDGE LN                                                                           */
    /*      IL        60527      6201 WILDWOOD LN                                                                             */
    /*      IL        60068      738 N WASHINGTON AVE                                                                         */
    /*      IL        62232      901 BEAR CREEK CT                                                                            */
    /*      IL        60637      5841 S MARYLAND AVE                                                                          */
    /*      IL        60611      680 N LAKE SHORE DR STE 1000                                                                 */
    /*      IL        60153      2160 S 1ST AVE BLDG 101 STE 1752                                                             */
    /*      IL        60611      225 E CHICAGO AVE                                                                            */
    /*      IL        60612      1900 W POLK ST                                                                               */
    /*      IL        62703      1025 S 6TH ST                                                                                */
    /*      IL        60612      1653 W CONGRESS BLVD                                                                         */
    /*      IL        62781      701 N 1ST ST                                                                                 */
    /*      IL        60506      1870 W GALENA BLVD                                                                           */
    /*      IL        60657      836 W WELLINGTON STE 7405                                                                    */
    /*      IL        60099      2520 ELISHA AVE                                                                              */
    /*      IL        60005      800 W CENTRAL RD                                                                             */
    /*      IL        60426      71 W 156TH ST                                                                                */
    /*      IL        61108      2550 CHARLES ST                                                                              */
    /*      IL        60805      2800 W 95TH ST 3N                                                                            */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*               _         _ _  _   _ _____ __     ____    _        _     _
     _   _ _ __  ___(_)_ __   / | || | / | ____/ /_   |___ \  | |_ __ _| |__ | | ___
    | | | | `_ \|_  / | `_ \  | | || |_| |  _|| `_ \    __) | | __/ _` | `_ \| |/ _ \
    | |_| | | | |/ /| | |_) | | |__   _| | |__| (_) |  / __/  | || (_| | |_) | |  __/
     \__,_|_| |_/___|_| .__/  |_|  |_| |_|_____\___/  |_____|  \__\__,_|_.__/|_|\___|
                      |_|
    */

    /*----                                                                   ----*/
    /*---- convert 141E6 address to sas/wps table                            ----*/
    /*----                                                                   ----*/

    data adr.adr_010adrlonlatfmt(compress=char);
       infile 'm:/sd1/adr_010adrlonlatfmt.csv' firstobs=2 delimiter=',' DSD lrecl=32767;
       informat MATCHCODE $64. ;
       informat ZIP4 $4. ;
       informat STATE $2. ;
       informat ADR $64. ;
       informat AVGLAT best12. ;
       informat AVGLON best12. ;
       input
          MATCHCODE
          ZIP4
          STATE
          ADR
          AVGLAT
          AVGLON
       ;
       if mod(_n_,2000000)=0 then put _n_;
    run;quit;

    /*           _             _   _            _ _  _   _ _____ __             _     _
    (_)_ __   __| | _____  __ | |_| |__   ___  / | || | / | ____/ /_   __ _  __| | __| |_ __ ___  ___ ___
    | | `_ \ / _` |/ _ \ \/ / | __| `_ \ / _ \ | | || |_| |  _|| `_ \ / _` |/ _` |/ _` | `__/ _ \/ __/ __|
    | | | | | (_| |  __/>  <  | |_| | | |  __/ | |__   _| | |__| (_) | (_| | (_| | (_| | | |  __/\__ \__ \
    |_|_| |_|\__,_|\___/_/\_\  \__|_| |_|\___| |_|  |_| |_|_____\___/ \__,_|\__,_|\__,_|_|  \___||___/___/
    */

    proc sort data=adr.adr_010adrlonlatfmt out=adr.adr_010adrlonlatsrt(index=(zip4)) noequals;
       by zip4 matchcode state adr;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* ADDRESSES CAN HAVE LEADING 0s                                                                                          */
    /*                                                                                                                        */
    /* SD1.ADR_010ADRLONLATFMT total obs=141,882,890                                                                          */
    /*                                                                                                                        */
    /*  MATCHCODE                         ZIP4    STATE    ADR (has zip5 on end)                   AVGLAT      AVGLON         */
    /*                                                                                                                        */
    /*  184BELLEST00002                   0000     IA      184 BELLE ST 00002                      -92.299    42.4518         */
    /*  2HALBROOK                         0000     TX      2 HALBROOK                              -95.880    32.6956         */
    /*  4518CHARMDR00001                  0000     IA      4518 CHARM DR 00001                     -92.415    42.4473         */
    /*  1481WROSEBUDRD00063               0006     MT      1481 W ROSEBUD RD 00063                -109.637    45.2817         */
    /*  10005AVE00083                     0008     NY      1000 5 AVE 00083                        -73.963    40.7784         */
    /*  10086STTRANSVERSE00083            0008     NY      100 86 ST TRANSVERSE 00083              -73.964    40.7834         */
    /*  10097STTRANSVERSE00083            0008     NY      100 97 ST TRANSVERSE 00083              -73.961    40.7889         */
    /*  10502CENTRALPKNEAR5AVE00083       0008     NY      10502 CENTRAL PKNEAR 5 AVE 00083        -73.953    40.7942         */
    /*  10651EDR00083                     0008     NY      10651 E DR 00083                        -73.955    40.7961         */
    /*  11097STTRANSVERSE00083            0008     NY      110 97 ST TRANSVERSE 00083              -73.961    40.7889         */
    /*  12279STTRANSVERSE00083            0008     NY      122 79 ST TRANSVERSE 00083              -73.968    40.7788         */
    /*  15197STTRANSVERSE00083            0008     NY      151 97 ST TRANSVERSE 00083              -73.960    40.7912         */
    /*  19086STTRANSVERSE00083            0008     NY      190 86 ST TRANSVERSE 00083              -73.966    40.7839         */
    /*  1CENTRALPARKHECKSCHERFLDS00083    0008     NY      1 CENTRAL PARK HECKSCHER FLDS 00083     -73.976    40.7703         */
    /*  2W69ST00083                       0008     NY      2 W 69 ST 00083                         -73.975    40.7731         */
    /*  34CENTRALPARKN00083               0008     NY      34 CENTRAL PARK N 00083                 -73.951    40.7972         */
    /*  4586STTRANSVERSE00083             0008     NY      45 86 ST TRANSVERSE 00083               -73.962    40.7822         */
    /*  50TERDR00083                      0008     NY      50 TER DR 00083                         -73.970    40.7729         */
    /*  6403CTRDR00083                    0008     NY      6403 CTR DR 00083                       -73.974    40.7691         */
    /*  6502CENTRALPKNEAR5AVE00083        0008     NY      6502 CENTRAL PKNEAR 5 AVE 00083         -73.971    40.7684         */
    /*  7401EDR00083                      0008     NY      7401 E DR 00083                         -73.969    40.7751         */
    /*  7402CENTRALPKNEAR5AVE00083        0008     NY      7402 CENTRAL PKNEAR 5 AVE 00083         -73.967    40.7740         */
    /*  7502EDR00083                      0008     NY      7502 E DR 00083                         -73.968    40.7757         */
    /*  7804EDR00083                      0008     NY      7804 E DR 00083                         -73.968    40.7784         */
    /*  7901WDR00083                      0008     NY      7901 W DR 00083                         -73.970    40.7800         */
    /*  7940WDR00083                      0008     NY      7940 W DR 00083                         -73.971    40.7805         */
    /*  8305AVE00083                      0008     NY      830 5 AVE 00083                         -73.971    40.7677         */
    /*  2NCLEARWATERAVE00099              0009     CA      2 N CLEARWATER AVE 00099               -117.376    34.2050         */
    /*  108SPENCERBALERD00126             0012     ME      108 SPENCER BALE RD 00126               -70.546    45.3830         */
    /*  10DOMEMTNCAMPSRD00126             0012     ME      10 DOME MTN CAMPS RD 00126              -70.684    45.5631         */
    /*  10KIBBYWINDARDGRD00126            0012     ME      10 KIBBY WIND A RDG RD 00126            -70.540    45.3887         */
    /*  11KIBBYWINDARDGRD00126            0012     ME      11 KIBBY WIND A RDG RD 00126            -70.544    45.4006         */
    /*  12KIBBYWINDARDGRD00126            0012     ME      12 KIBBY WIND A RDG RD 00126            -70.547    45.3988         */
    /*  13KIBBYWINDARDGRD00126            0012     ME      13 KIBBY WIND A RDG RD 00126            -70.549    45.3966         */
    /*  14KIBBYWINDARDGRD00126            0012     ME      14 KIBBY WIND A RDG RD 00126            -70.524    45.3931         */
    /*  1543DAAQUAMRD00125                0012     ME      1543 DAAQUAM RD 00125                   -69.793    46.6034         */
    /*  1545DAAQUAMRD00125                0012     ME      1545 DAAQUAM RD 00125                   -69.817    46.5809         */
    /*  15HEMLOCKLN00127                  0012     ME      15 HEMLOCK LN 00127                     -68.091    45.2164         */
    /*  15KIBBYWINDARDGRD00126            0012     ME      15 KIBBY WIND A RDG RD 00126            -70.524    45.3908         */
    /*  16KIBBYWINDARDGRD00126            0012     ME      16 KIBBY WIND A RDG RD 00126            -70.524    45.3882         */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*    _                                                    _     _
      ___| | ___  __ _ _ __    _ __   _____      __   __ _  __| | __| |_ __ ___  ___ ___  ___  ___
     / __| |/ _ \/ _` | `_ \  | `_ \ / _ \ \ /\ / /  / _` |/ _` |/ _` | `__/ _ \/ __/ __|/ _ \/ __|
    | (__| |  __/ (_| | | | | | | | |  __/\ V  V /  | (_| | (_| | (_| | | |  __/\__ \__ \  __/\__ \
     \___|_|\___|\__,_|_| |_| |_| |_|\___| \_/\_/    \__,_|\__,_|\__,_|_|  \___||___/___/\___||___/

    */

    /*   _                  _               _              __  __ _
     ___| |_ __ _ _ __   __| | __ _ _ __ __| |  ___ _   _ / _|/ _(_)_  __
    / __| __/ _` | `_ \ / _` |/ _` | `__/ _` | / __| | | | |_| |_| \ \/ /
    \__ \ || (_| | | | | (_| | (_| | | | (_| | \__ \ |_| |  _|  _| |>  <
    |___/\__\__,_|_| |_|\__,_|\__,_|_|  \__,_| |___/\__,_|_| |_| |_/_/\_\

    */

    filename ft15f001 "d:/adr/sas/adr_010sfx.sas";
    parmcards4;
    when (strip(sfx)='BOULEVARD') substr(adr,length(adr)-len) =' BLVD';
    when (strip(sfx)='HARBOR') substr(adr,length(adr)-len) =' HBR';
    when (strip(sfx)='TRPK') substr(adr,length(adr)-len) =' TPKE';
    when (strip(sfx)='FORGES') substr(adr,length(adr)-len) =' FRGS';
    when (strip(sfx)='BYPAS') substr(adr,length(adr)-len) =' BYP';
    when (strip(sfx)='VIADUCT') substr(adr,length(adr)-len) =' VIA';
    when (strip(sfx)='MNT') substr(adr,length(adr)-len) =' MT';
    when (strip(sfx)='LNDNG') substr(adr,length(adr)-len) =' LNDG';
    when (strip(sfx)='VILL') substr(adr,length(adr)-len) =' VLG';
    when (strip(sfx)='MILL') substr(adr,length(adr)-len) =' ML';
    when (strip(sfx)='CENTERS') substr(adr,length(adr)-len) =' CTRS';
    when (strip(sfx)='CNTER') substr(adr,length(adr)-len) =' CTR';
    when (strip(sfx)='HRBOR') substr(adr,length(adr)-len) =' HBR';
    when (strip(sfx)='TR') substr(adr,length(adr)-len) =' TRL';
    when (strip(sfx)='PASSAGE') substr(adr,length(adr)-len) =' PSGE';
    when (strip(sfx)='WALKS') substr(adr,length(adr)-len) =' WALK';
    when (strip(sfx)='CREST') substr(adr,length(adr)-len) =' CRST';
    when (strip(sfx)='MEADOWS') substr(adr,length(adr)-len) =' MDWS';
    when (strip(sfx)='FREEWY') substr(adr,length(adr)-len) =' FWY';
    when (strip(sfx)='GARDEN') substr(adr,length(adr)-len) =' GDN';
    when (strip(sfx)='BLUFFS') substr(adr,length(adr)-len) =' BLFS';
    when (strip(sfx)='TRK') substr(adr,length(adr)-len) =' TRAK';
    when (strip(sfx)='SQUARES') substr(adr,length(adr)-len) =' SQS';
    when (strip(sfx)='FRRY') substr(adr,length(adr)-len) =' FRY';
    when (strip(sfx)='DIV') substr(adr,length(adr)-len) =' DV';
    when (strip(sfx)='STRAVEN') substr(adr,length(adr)-len) =' STRA';
    when (strip(sfx)='CMP') substr(adr,length(adr)-len) =' CP';
    when (strip(sfx)='GRDNS') substr(adr,length(adr)-len) =' GDNS';
    when (strip(sfx)='VILLG') substr(adr,length(adr)-len) =' VLG';
    when (strip(sfx)='MEADOW') substr(adr,length(adr)-len) =' MDW';
    when (strip(sfx)='TRAILS') substr(adr,length(adr)-len) =' TRL';
    when (strip(sfx)='STREETS') substr(adr,length(adr)-len) =' STS';
    when (strip(sfx)='PRAIRIE') substr(adr,length(adr)-len) =' PR';
    when (strip(sfx)='CRESCENT') substr(adr,length(adr)-len) =' CRES';
    when (strip(sfx)='PORT') substr(adr,length(adr)-len) =' PRT';
    when (strip(sfx)='BLUF') substr(adr,length(adr)-len) =' BLF';
    when (strip(sfx)='AVNUE') substr(adr,length(adr)-len) =' AVE';
    when (strip(sfx)='LIGHTS') substr(adr,length(adr)-len) =' LGTS';
    when (strip(sfx)='HARBORS') substr(adr,length(adr)-len) =' HBRS';
    when (strip(sfx)='LODG') substr(adr,length(adr)-len) =' LDG';
    when (strip(sfx)='TRACKS') substr(adr,length(adr)-len) =' TRAK';
    when (strip(sfx)='PKWAY') substr(adr,length(adr)-len) =' PKWY';
    when (strip(sfx)='BOT') substr(adr,length(adr)-len) =' BTM';
    when (strip(sfx)='DRV') substr(adr,length(adr)-len) =' DR';
    when (strip(sfx)='DIVIDE') substr(adr,length(adr)-len) =' DV';
    when (strip(sfx)='FORDS') substr(adr,length(adr)-len) =' FRDS';
    when (strip(sfx)='AVENU') substr(adr,length(adr)-len) =' AVE';
    when (strip(sfx)='RIVR') substr(adr,length(adr)-len) =' RIV';
    when (strip(sfx)='GATEWAY') substr(adr,length(adr)-len) =' GTWY';
    when (strip(sfx)='STREAM') substr(adr,length(adr)-len) =' STRM';
    when (strip(sfx)='BAYOO') substr(adr,length(adr)-len) =' BYU';
    when (strip(sfx)='KNOLL') substr(adr,length(adr)-len) =' KNL';
    when (strip(sfx)='EXPRESSWAY') substr(adr,length(adr)-len) =' EXPY';
    when (strip(sfx)='SPRNG') substr(adr,length(adr)-len) =' SPG';
    when (strip(sfx)='FLAT') substr(adr,length(adr)-len) =' FLT';
    when (strip(sfx)='GRDEN') substr(adr,length(adr)-len) =' GDN';
    when (strip(sfx)='TRAIL') substr(adr,length(adr)-len) =' TRL';
    when (strip(sfx)='JCTNS') substr(adr,length(adr)-len) =' JCTS';
    when (strip(sfx)='TUNNEL') substr(adr,length(adr)-len) =' TUNL';
    when (strip(sfx)='GROVES') substr(adr,length(adr)-len) =' GRVS';
    when (strip(sfx)='VALLY') substr(adr,length(adr)-len) =' VLY';
    when (strip(sfx)='FERRY') substr(adr,length(adr)-len) =' FRY';
    when (strip(sfx)='PARKWAY') substr(adr,length(adr)-len) =' PKWY';
    when (strip(sfx)='RADIEL') substr(adr,length(adr)-len) =' RADL';
    when (strip(sfx)='STRVNUE') substr(adr,length(adr)-len) =' STRA';
    when (strip(sfx)='OVERPASS') substr(adr,length(adr)-len) =' OPAS';
    when (strip(sfx)='PLAZA') substr(adr,length(adr)-len) =' PLZ';
    when (strip(sfx)='ESTATE') substr(adr,length(adr)-len) =' EST';
    when (strip(sfx)='MNTN') substr(adr,length(adr)-len) =' MTN';
    when (strip(sfx)='LOCK') substr(adr,length(adr)-len) =' LCK';
    when (strip(sfx)='ORCHRD') substr(adr,length(adr)-len) =' ORCH';
    when (strip(sfx)='STRVN') substr(adr,length(adr)-len) =' STRA';
    when (strip(sfx)='LOCKS') substr(adr,length(adr)-len) =' LCKS';
    when (strip(sfx)='BEND') substr(adr,length(adr)-len) =' BND';
    when (strip(sfx)='JUNCTIONS') substr(adr,length(adr)-len) =' JCTS';
    when (strip(sfx)='MOUNTIN') substr(adr,length(adr)-len) =' MTN';
    when (strip(sfx)='BURGS') substr(adr,length(adr)-len) =' BGS';
    when (strip(sfx)='PINE') substr(adr,length(adr)-len) =' PNE';
    when (strip(sfx)='LDGE') substr(adr,length(adr)-len) =' LDG';
    when (strip(sfx)='CAUSWAY') substr(adr,length(adr)-len) =' CSWY';
    when (strip(sfx)='BEACH') substr(adr,length(adr)-len) =' BCH';
    when (strip(sfx)='MOTORWAY') substr(adr,length(adr)-len) =' MTWY';
    when (strip(sfx)='BLUFF') substr(adr,length(adr)-len) =' BLF';
    when (strip(sfx)='COURT') substr(adr,length(adr)-len) =' CT';
    when (strip(sfx)='GROV') substr(adr,length(adr)-len) =' GRV';
    when (strip(sfx)='SPRNGS') substr(adr,length(adr)-len) =' SPGS';
    when (strip(sfx)='OVL') substr(adr,length(adr)-len) =' OVAL';
    when (strip(sfx)='VILLAG') substr(adr,length(adr)-len) =' VLG';
    when (strip(sfx)='VDCT') substr(adr,length(adr)-len) =' VIA';
    when (strip(sfx)='NECK') substr(adr,length(adr)-len) =' NCK';
    when (strip(sfx)='ORCHARD') substr(adr,length(adr)-len) =' ORCH';
    when (strip(sfx)='LIGHT') substr(adr,length(adr)-len) =' LGT';
    when (strip(sfx)='SHORE') substr(adr,length(adr)-len) =' SHR';
    when (strip(sfx)='GREEN') substr(adr,length(adr)-len) =' GRN';
    when (strip(sfx)='ISLND') substr(adr,length(adr)-len) =' IS';
    when (strip(sfx)='TURNPIKE') substr(adr,length(adr)-len) =' TPKE';
    when (strip(sfx)='MISSION') substr(adr,length(adr)-len) =' MSN';
    when (strip(sfx)='SPNGS') substr(adr,length(adr)-len) =' SPGS';
    when (strip(sfx)='COURSE') substr(adr,length(adr)-len) =' CRSE';
    when (strip(sfx)='TRAFFICWAY') substr(adr,length(adr)-len) =' TRFY';
    when (strip(sfx)='TERRACE') substr(adr,length(adr)-len) =' TER';
    when (strip(sfx)='HWAY') substr(adr,length(adr)-len) =' HWY';
    when (strip(sfx)='AVENUE') substr(adr,length(adr)-len) =' AVE';
    when (strip(sfx)='GLEN') substr(adr,length(adr)-len) =' GLN';
    when (strip(sfx)='BOUL') substr(adr,length(adr)-len) =' BLVD';
    when (strip(sfx)='INLET') substr(adr,length(adr)-len) =' INLT';
    when (strip(sfx)='LA') substr(adr,length(adr)-len) =' LN';
    when (strip(sfx)='BROOK') substr(adr,length(adr)-len) =' BRK';
    when (strip(sfx)='SHOAR') substr(adr,length(adr)-len) =' SHR';
    when (strip(sfx)='BYPASS') substr(adr,length(adr)-len) =' BYP';
    when (strip(sfx)='MTIN') substr(adr,length(adr)-len) =' MTN';
    when (strip(sfx)='ALLY') substr(adr,length(adr)-len) =' ALY';
    when (strip(sfx)='FOREST') substr(adr,length(adr)-len) =' FRST';
    when (strip(sfx)='JUNCTION') substr(adr,length(adr)-len) =' JCT';
    when (strip(sfx)='VIEWS') substr(adr,length(adr)-len) =' VWS';
    when (strip(sfx)='WELLS') substr(adr,length(adr)-len) =' WLS';
    when (strip(sfx)='CEN') substr(adr,length(adr)-len) =' CTR';
    when (strip(sfx)='CRT') substr(adr,length(adr)-len) =' CT';
    when (strip(sfx)='CORNERS') substr(adr,length(adr)-len) =' CORS';
    when (strip(sfx)='FRWAY') substr(adr,length(adr)-len) =' FWY';
    when (strip(sfx)='PRARIE') substr(adr,length(adr)-len) =' PR';
    when (strip(sfx)='CROSSING') substr(adr,length(adr)-len) =' XING';
    when (strip(sfx)='EXTN') substr(adr,length(adr)-len) =' EXT';
    when (strip(sfx)='CLIFFS') substr(adr,length(adr)-len) =' CLFS';
    when (strip(sfx)='MANORS') substr(adr,length(adr)-len) =' MNRS';
    when (strip(sfx)='PORTS') substr(adr,length(adr)-len) =' PRTS';
    when (strip(sfx)='GATEWY') substr(adr,length(adr)-len) =' GTWY';
    when (strip(sfx)='SQUARE') substr(adr,length(adr)-len) =' SQ';
    when (strip(sfx)='HARB') substr(adr,length(adr)-len) =' HBR';
    when (strip(sfx)='LOOPS') substr(adr,length(adr)-len) =' LOOP';
    when (strip(sfx)='HILL') substr(adr,length(adr)-len) =' HL';
    when (strip(sfx)='HIGHWAY') substr(adr,length(adr)-len) =' HWY';
    when (strip(sfx)='BROOKS') substr(adr,length(adr)-len) =' BRKS';
    when (strip(sfx)='BRNCH') substr(adr,length(adr)-len) =' BR';
    when (strip(sfx)='AVEN') substr(adr,length(adr)-len) =' AVE';
    when (strip(sfx)='SHORES') substr(adr,length(adr)-len) =' SHRS';
    when (strip(sfx)='ROUTE') substr(adr,length(adr)-len) =' RTE';
    when (strip(sfx)='PLACE') substr(adr,length(adr)-len) =' PL';
    when (strip(sfx)='SUMIT') substr(adr,length(adr)-len) =' SMT';
    when (strip(sfx)='PINES') substr(adr,length(adr)-len) =' PNES';
    when (strip(sfx)='TRKS') substr(adr,length(adr)-len) =' TRAK';
    when (strip(sfx)='SHOAL') substr(adr,length(adr)-len) =' SHL';
    when (strip(sfx)='STRT') substr(adr,length(adr)-len) =' ST';
    when (strip(sfx)='FRWY') substr(adr,length(adr)-len) =' FWY';
    when (strip(sfx)='HEIGHTS') substr(adr,length(adr)-len) =' HTS';
    when (strip(sfx)='RANCHES') substr(adr,length(adr)-len) =' RNCH';
    when (strip(sfx)='BOULEVARD') substr(adr,length(adr)-len) =' BLVD';
    when (strip(sfx)='EXTNSN') substr(adr,length(adr)-len) =' EXT';
    when (strip(sfx)='HOLLOWS') substr(adr,length(adr)-len) =' HOLW';
    when (strip(sfx)='VSTA') substr(adr,length(adr)-len) =' VIS';
    when (strip(sfx)='PLAINS') substr(adr,length(adr)-len) =' PLNS';
    when (strip(sfx)='STATION') substr(adr,length(adr)-len) =' STA';
    when (strip(sfx)='CIRCL') substr(adr,length(adr)-len) =' CIR';
    when (strip(sfx)='MNTNS') substr(adr,length(adr)-len) =' MTNS';
    when (strip(sfx)='VILLAGES') substr(adr,length(adr)-len) =' VLGS';
    when (strip(sfx)='HAVEN') substr(adr,length(adr)-len) =' HVN';
    when (strip(sfx)='TURNPK') substr(adr,length(adr)-len) =' TPKE';
    when (strip(sfx)='EXPR') substr(adr,length(adr)-len) =' EXPY';
    when (strip(sfx)='STN') substr(adr,length(adr)-len) =' STA';
    when (strip(sfx)='EXPW') substr(adr,length(adr)-len) =' EXPY';
    when (strip(sfx)='STREET') substr(adr,length(adr)-len) =' ST';
    when (strip(sfx)='STR') substr(adr,length(adr)-len) =' ST';
    when (strip(sfx)='SPURS') substr(adr,length(adr)-len) =' SPUR';
    when (strip(sfx)='CRECENT') substr(adr,length(adr)-len) =' CRES';
    when (strip(sfx)='RAD') substr(adr,length(adr)-len) =' RADL';
    when (strip(sfx)='RANCH') substr(adr,length(adr)-len) =' RNCH';
    when (strip(sfx)='WELL') substr(adr,length(adr)-len) =' WL';
    when (strip(sfx)='SHOALS') substr(adr,length(adr)-len) =' SHLS';
    when (strip(sfx)='ALLEY') substr(adr,length(adr)-len) =' ALY';
    when (strip(sfx)='PLZA') substr(adr,length(adr)-len) =' PLZ';
    when (strip(sfx)='MEDOWS') substr(adr,length(adr)-len) =' MDWS';
    when (strip(sfx)='ALLEE') substr(adr,length(adr)-len) =' ALY';
    when (strip(sfx)='HAVN') substr(adr,length(adr)-len) =' HVN';
    when (strip(sfx)='PATHS') substr(adr,length(adr)-len) =' PATH';
    when (strip(sfx)='BYPA') substr(adr,length(adr)-len) =' BYP';
    when (strip(sfx)='MILLS') substr(adr,length(adr)-len) =' MLS';
    when (strip(sfx)='PARKS') substr(adr,length(adr)-len) =' PARK';
    when (strip(sfx)='BYPS') substr(adr,length(adr)-len) =' BYP';
    when (strip(sfx)='TUNNELS') substr(adr,length(adr)-len) =' TUNL';
    when (strip(sfx)='CLUB') substr(adr,length(adr)-len) =' CLB';
    when (strip(sfx)='SQRS') substr(adr,length(adr)-len) =' SQS';
    when (strip(sfx)='HLLW') substr(adr,length(adr)-len) =' HOLW';
    when (strip(sfx)='MANOR') substr(adr,length(adr)-len) =' MNR';
    when (strip(sfx)='CENTRE') substr(adr,length(adr)-len) =' CTR';
    when (strip(sfx)='TRACK') substr(adr,length(adr)-len) =' TRAK';
    when (strip(sfx)='HGTS') substr(adr,length(adr)-len) =' HTS';
    when (strip(sfx)='CRCLE') substr(adr,length(adr)-len) =' CIR';
    when (strip(sfx)='FALLS') substr(adr,length(adr)-len) =' FLS';
    when (strip(sfx)='LANDING') substr(adr,length(adr)-len) =' LNDG';
    when (strip(sfx)='PLAINES') substr(adr,length(adr)-len) =' PLNS';
    when (strip(sfx)='VIADCT') substr(adr,length(adr)-len) =' VIA';
    when (strip(sfx)='GROVE') substr(adr,length(adr)-len) =' GRV';
    when (strip(sfx)='CAMP') substr(adr,length(adr)-len) =' CP';
    when (strip(sfx)='TPK') substr(adr,length(adr)-len) =' TPKE';
    when (strip(sfx)='DRIVE') substr(adr,length(adr)-len) =' DR';
    when (strip(sfx)='FREEWAY') substr(adr,length(adr)-len) =' FWY';
    when (strip(sfx)='POINTS') substr(adr,length(adr)-len) =' PTS';
    when (strip(sfx)='EXP') substr(adr,length(adr)-len) =' EXPY';
    when (strip(sfx)='COURTS') substr(adr,length(adr)-len) =' CTS';
    when (strip(sfx)='PKY') substr(adr,length(adr)-len) =' PKWY';
    when (strip(sfx)='CORNER') substr(adr,length(adr)-len) =' COR';
    when (strip(sfx)='CRSSING') substr(adr,length(adr)-len) =' XING';
    when (strip(sfx)='UNIONS') substr(adr,length(adr)-len) =' UNS';
    when (strip(sfx)='LODGE') substr(adr,length(adr)-len) =' LDG';
    when (strip(sfx)='CIRCLE') substr(adr,length(adr)-len) =' CIR';
    when (strip(sfx)='BRIDGE') substr(adr,length(adr)-len) =' BRG';
    when (strip(sfx)='EXPRESS') substr(adr,length(adr)-len) =' EXPY';
    when (strip(sfx)='TUNLS') substr(adr,length(adr)-len) =' TUNL';
    when (strip(sfx)='KNOLLS') substr(adr,length(adr)-len) =' KNLS';
    when (strip(sfx)='GREENS') substr(adr,length(adr)-len) =' GRNS';
    when (strip(sfx)='TUNEL') substr(adr,length(adr)-len) =' TUNL';
    when (strip(sfx)='FIELDS') substr(adr,length(adr)-len) =' FLDS';
    when (strip(sfx)='COMMON') substr(adr,length(adr)-len) =' CMN';
    when (strip(sfx)='RIVER') substr(adr,length(adr)-len) =' RIV';
    when (strip(sfx)='VIEW') substr(adr,length(adr)-len) =' VW';
    when (strip(sfx)='CRSENT') substr(adr,length(adr)-len) =' CRES';
    when (strip(sfx)='RNCHS') substr(adr,length(adr)-len) =' RNCH';
    when (strip(sfx)='CRSCNT') substr(adr,length(adr)-len) =' CRES';
    when (strip(sfx)='RDGE') substr(adr,length(adr)-len) =' RDG';
    when (strip(sfx)='CAUSEWAY') substr(adr,length(adr)-len) =' CSWY';
    when (strip(sfx)='PARKWY') substr(adr,length(adr)-len) =' PKWY';
    when (strip(sfx)='JUNCTON') substr(adr,length(adr)-len) =' JCT';
    when (strip(sfx)='STATN') substr(adr,length(adr)-len) =' STA';
    when (strip(sfx)='GARDN') substr(adr,length(adr)-len) =' GDN';
    when (strip(sfx)='MNTAIN') substr(adr,length(adr)-len) =' MTN';
    when (strip(sfx)='CRSSNG') substr(adr,length(adr)-len) =' XING';
    when (strip(sfx)='RAPID') substr(adr,length(adr)-len) =' RPD';
    when (strip(sfx)='KEY') substr(adr,length(adr)-len) =' KY';
    when (strip(sfx)='WY') substr(adr,length(adr)-len) =' WAY';
    when (strip(sfx)='THROUGHWAY') substr(adr,length(adr)-len) =' TRWY';
    when (strip(sfx)='ESTATES') substr(adr,length(adr)-len) =' ESTS';
    when (strip(sfx)='CK') substr(adr,length(adr)-len) =' CRK';
    when (strip(sfx)='LOAF') substr(adr,length(adr)-len) =' LF';
    when (strip(sfx)='HOLLOW') substr(adr,length(adr)-len) =' HOLW';
    when (strip(sfx)='CANYON') substr(adr,length(adr)-len) =' CYN';
    when (strip(sfx)='VILLAGE') substr(adr,length(adr)-len) =' VLG';
    when (strip(sfx)='CR') substr(adr,length(adr)-len) =' CRK';
    when (strip(sfx)='CT') substr(adr,length(adr)-len) =' CTS';
    when (strip(sfx)='JCTION') substr(adr,length(adr)-len) =' JCT';
    when (strip(sfx)='MSSN') substr(adr,length(adr)-len) =' MSN';
    when (strip(sfx)='BRDGE') substr(adr,length(adr)-len) =' BRG';
    when (strip(sfx)='CENT') substr(adr,length(adr)-len) =' CTR';
    when (strip(sfx)='FRT') substr(adr,length(adr)-len) =' FT';
    when (strip(sfx)='PK') substr(adr,length(adr)-len) =' PARK';
    when (strip(sfx)='LANES') substr(adr,length(adr)-len) =' LN';
    when (strip(sfx)='GTWAY') substr(adr,length(adr)-len) =' GTWY';
    when (strip(sfx)='PRK') substr(adr,length(adr)-len) =' PARK';
    when (strip(sfx)='STRAVENUE') substr(adr,length(adr)-len) =' STRA';
    when (strip(sfx)='HIWAY') substr(adr,length(adr)-len) =' HWY';
    when (strip(sfx)='VILLE') substr(adr,length(adr)-len) =' VL';
    when (strip(sfx)='PLAIN') substr(adr,length(adr)-len) =' PLN';
    when (strip(sfx)='MOUNT') substr(adr,length(adr)-len) =' MT';
    when (strip(sfx)='CENTR') substr(adr,length(adr)-len) =' CTR';
    when (strip(sfx)='PRR') substr(adr,length(adr)-len) =' PR';
    when (strip(sfx)='AVN') substr(adr,length(adr)-len) =' AVE';
    when (strip(sfx)='SPNG') substr(adr,length(adr)-len) =' SPG';
    when (strip(sfx)='HIWY') substr(adr,length(adr)-len) =' HWY';
    when (strip(sfx)='DAM') substr(adr,length(adr)-len) =' DM';
    when (strip(sfx)='CRCL') substr(adr,length(adr)-len) =' CIR';
    when (strip(sfx)='SQRE') substr(adr,length(adr)-len) =' SQ';
    when (strip(sfx)='JCTN') substr(adr,length(adr)-len) =' JCT';
    when (strip(sfx)='MOUNTAIN') substr(adr,length(adr)-len) =' MTN';
    when (strip(sfx)='KEYS') substr(adr,length(adr)-len) =' KYS';
    when (strip(sfx)='PARKWAYS') substr(adr,length(adr)-len) =' PKWY';
    when (strip(sfx)='DRIVES') substr(adr,length(adr)-len) =' DRS';
    when (strip(sfx)='CENTER') substr(adr,length(adr)-len) =' CTR';
    when (strip(sfx)='DRIV') substr(adr,length(adr)-len) =' DR';
    when (strip(sfx)='SUMITT') substr(adr,length(adr)-len) =' SMT';
    when (strip(sfx)='CANYN') substr(adr,length(adr)-len) =' CYN';
    when (strip(sfx)='HARBR') substr(adr,length(adr)-len) =' HBR';
    when (strip(sfx)='REST') substr(adr,length(adr)-len) =' RST';
    when (strip(sfx)='SHOARS') substr(adr,length(adr)-len) =' SHRS';
    when (strip(sfx)='VIST') substr(adr,length(adr)-len) =' VIS';
    when (strip(sfx)='ISLNDS') substr(adr,length(adr)-len) =' ISS';
    when (strip(sfx)='HILLS') substr(adr,length(adr)-len) =' HLS';
    when (strip(sfx)='CRESENT') substr(adr,length(adr)-len) =' CRES';
    when (strip(sfx)='POINT') substr(adr,length(adr)-len) =' PT';
    when (strip(sfx)='LAKE') substr(adr,length(adr)-len) =' LK';
    when (strip(sfx)='VLLY') substr(adr,length(adr)-len) =' VLY';
    when (strip(sfx)='STRAV') substr(adr,length(adr)-len) =' STRA';
    when (strip(sfx)='CROSSROAD') substr(adr,length(adr)-len) =' XRD';
    when (strip(sfx)='STRAVE') substr(adr,length(adr)-len) =' STRA';
    when (strip(sfx)='STRAVN') substr(adr,length(adr)-len) =' STRA';
    when (strip(sfx)='KNOL') substr(adr,length(adr)-len) =' KNL';
    when (strip(sfx)='FORGE') substr(adr,length(adr)-len) =' FRG';
    when (strip(sfx)='CNTR') substr(adr,length(adr)-len) =' CTR';
    when (strip(sfx)='CAPE') substr(adr,length(adr)-len) =' CPE';
    when (strip(sfx)='HEIGHT') substr(adr,length(adr)-len) =' HTS';
    when (strip(sfx)='HIGHWY') substr(adr,length(adr)-len) =' HWY';
    when (strip(sfx)='TRNPK') substr(adr,length(adr)-len) =' TPKE';
    when (strip(sfx)='BOULV') substr(adr,length(adr)-len) =' BLVD';
    when (strip(sfx)='CIRCLES') substr(adr,length(adr)-len) =' CIRS';
    when (strip(sfx)='VALLEYS') substr(adr,length(adr)-len) =' VLYS';
    when (strip(sfx)='VST') substr(adr,length(adr)-len) =' VIS';
    when (strip(sfx)='CREEK') substr(adr,length(adr)-len) =' CRK';
    when (strip(sfx)='SPRING') substr(adr,length(adr)-len) =' SPG';
    when (strip(sfx)='HOLWS') substr(adr,length(adr)-len) =' HOLW';
    when (strip(sfx)='TRACE') substr(adr,length(adr)-len) =' TRCE';
    when (strip(sfx)='BOTTOM') substr(adr,length(adr)-len) =' BTM';
    when (strip(sfx)='STREME') substr(adr,length(adr)-len) =' STRM';
    when (strip(sfx)='ISLES') substr(adr,length(adr)-len) =' ISLE';
    when (strip(sfx)='CIRC') substr(adr,length(adr)-len) =' CIR';
    when (strip(sfx)='FORKS') substr(adr,length(adr)-len) =' FRKS';
    when (strip(sfx)='BURG') substr(adr,length(adr)-len) =' BG';
    when (strip(sfx)='TRLS') substr(adr,length(adr)-len) =' TRL';
    when (strip(sfx)='RADIAL') substr(adr,length(adr)-len) =' RADL';
    when (strip(sfx)='LAKES') substr(adr,length(adr)-len) =' LKS';
    when (strip(sfx)='EXTENSION') substr(adr,length(adr)-len) =' EXT';
    when (strip(sfx)='ISLAND') substr(adr,length(adr)-len) =' IS';
    when (strip(sfx)='TERR') substr(adr,length(adr)-len) =' TER';
    when (strip(sfx)='UNION') substr(adr,length(adr)-len) =' UN';
    when (strip(sfx)='EXTENSIONS') substr(adr,length(adr)-len) =' EXTS';
    when (strip(sfx)='PKWYS') substr(adr,length(adr)-len) =' PKWY';
    when (strip(sfx)='ISLANDS') substr(adr,length(adr)-len) =' ISS';
    when (strip(sfx)='ROAD') substr(adr,length(adr)-len) =' RD';
    when (strip(sfx)='ROADS') substr(adr,length(adr)-len) =' RDS';
    when (strip(sfx)='GLENS') substr(adr,length(adr)-len) =' GLNS';
    when (strip(sfx)='SPRINGS') substr(adr,length(adr)-len) =' SPGS';
    when (strip(sfx)='MISSN') substr(adr,length(adr)-len) =' MSN';
    when (strip(sfx)='RIDGE') substr(adr,length(adr)-len) =' RDG';
    when (strip(sfx)='ARCADE') substr(adr,length(adr)-len) =' ARC';
    when (strip(sfx)='BAYOU') substr(adr,length(adr)-len) =' BYU';
    when (strip(sfx)='CRSNT') substr(adr,length(adr)-len) =' CRES';
    when (strip(sfx)='JUNCTN') substr(adr,length(adr)-len) =' JCT';
    when (strip(sfx)='VALLEY') substr(adr,length(adr)-len) =' VLY';
    when (strip(sfx)='FORK') substr(adr,length(adr)-len) =' FRK';
    when (strip(sfx)='MOUNTAINS') substr(adr,length(adr)-len) =' MTNS';
    when (strip(sfx)='BOTTM') substr(adr,length(adr)-len) =' BTM';
    when (strip(sfx)='FORG') substr(adr,length(adr)-len) =' FRG';
    when (strip(sfx)='HT') substr(adr,length(adr)-len) =' HTS';
    when (strip(sfx)='FORD') substr(adr,length(adr)-len) =' FRD';
    when (strip(sfx)='GRDN') substr(adr,length(adr)-len) =' GDN';
    when (strip(sfx)='FORT') substr(adr,length(adr)-len) =' FT';
    when (strip(sfx)='TRACES') substr(adr,length(adr)-len) =' TRCE';
    when (strip(sfx)='CNYN') substr(adr,length(adr)-len) =' CYN';
    when (strip(sfx)='FLATS') substr(adr,length(adr)-len) =' FLTS';
    when (strip(sfx)='ANEX') substr(adr,length(adr)-len) =' ANX';
    when (strip(sfx)='GATWAY') substr(adr,length(adr)-len) =' GTWY';
    when (strip(sfx)='RAPIDS') substr(adr,length(adr)-len) =' RPDS';
    when (strip(sfx)='VILLIAGE') substr(adr,length(adr)-len) =' VLG';
    when (strip(sfx)='COVES') substr(adr,length(adr)-len) =' CVS';
    when (strip(sfx)='RVR') substr(adr,length(adr)-len) =' RIV';
    when (strip(sfx)='AV') substr(adr,length(adr)-len) =' AVE';
    when (strip(sfx)='PIKES') substr(adr,length(adr)-len) =' PIKE';
    when (strip(sfx)='VISTA') substr(adr,length(adr)-len) =' VIS';
    when (strip(sfx)='FORESTS') substr(adr,length(adr)-len) =' FRST';
    when (strip(sfx)='FIELD') substr(adr,length(adr)-len) =' FLD';
    when (strip(sfx)='BRANCH') substr(adr,length(adr)-len) =' BR';
    when (strip(sfx)='DALE') substr(adr,length(adr)-len) =' DL';
    when (strip(sfx)='ANNEX') substr(adr,length(adr)-len) =' ANX';
    when (strip(sfx)='SQR') substr(adr,length(adr)-len) =' SQ';
    when (strip(sfx)='COVE') substr(adr,length(adr)-len) =' CV';
    when (strip(sfx)='SQU') substr(adr,length(adr)-len) =' SQ';
    when (strip(sfx)='SKYWAY') substr(adr,length(adr)-len) =' SKWY';
    when (strip(sfx)='RIDGES') substr(adr,length(adr)-len) =' RDGS';
    when (strip(sfx)='TUNNL') substr(adr,length(adr)-len) =' TUNL';
    when (strip(sfx)='UNDERPASS') substr(adr,length(adr)-len) =' UPAS';
    when (strip(sfx)='CLIFF') substr(adr,length(adr)-len) =' CLF';
    when (strip(sfx)='LANE') substr(adr,length(adr)-len) =' LN';
    when (strip(sfx)='DVD') substr(adr,length(adr)-len) =' DV';
    when (strip(sfx)='CURVE') substr(adr,length(adr)-len) =' CURV';
    when (strip(sfx)='SUMMIT') substr(adr,length(adr)-len) =' SMT';
    when (strip(sfx)='GARDENS') substr(adr,length(adr)-len) =' GDNS';
    ;;;;
    run;quit;

    /*       _                        _                         _       _                  _               _
    (_)_ __ | |_ ___ _ __ _ __   __ _| | __      _____  _ __ __| |  ___| |_ __ _ _ __   __| | __ _ _ __ __| |
    | | `_ \| __/ _ \ `__| `_ \ / _` | | \ \ /\ / / _ \| `__/ _` | / __| __/ _` | `_ \ / _` |/ _` | `__/ _` |
    | | | | | ||  __/ |  | | | | (_| | |  \ V  V / (_) | | | (_| | \__ \ || (_| | | | | (_| | (_| | | | (_| |
    |_|_| |_|\__\___|_|  |_| |_|\__,_|_|   \_/\_/ \___/|_|  \__,_| |___/\__\__,_|_| |_|\__,_|\__,_|_|  \__,_|

    */

    filename ft15f001 "d:/adr/sas/adr_010sx1.sas";
    parmcards4;
         select;
            when (index(adr,' STREET '))      adr=tranwrd(adr,' STREET '     , ' ST '       );
            when (index(adr,' AVENUE '))      adr=tranwrd(adr,' AVENUE '     , ' AVE '      );
            when (index(adr,' ROAD '))        adr=tranwrd(adr,' ROAD '       , ' RD '       );
            when (index(adr,' BOULEVARD '))   adr=tranwrd(adr,' BOULEVARD '  , ' BLVD '     );
            when (index(adr,' VALLEY '))      adr=tranwrd(adr,' VALLEY '     , ' VLY '      );
            when (index(adr,' CNTER '))       adr=tranwrd(adr,' CNTER '      , ' CTR '      );
            when (index(adr,' ROUTE '))       adr=tranwrd(adr,' ROUTE '      , ' RTE '      );
            when (index(adr,' HEIGHTS '))     adr=tranwrd(adr,' HEIGHTS '    , ' HTS '      );
            when (index(adr,' EXPRESSWAY '))  adr=tranwrd(adr,' EXPRESSWAY ' , ' EXPY '     );
            when (index(adr,' CREEK '))       adr=tranwrd(adr,' CREEK '      , ' CRK '      );
            when (index(adr,' CIRCL '))       adr=tranwrd(adr,' CIRCL '      , ' CIR '      );
            when (index(adr,' TRPK '))        adr=tranwrd(adr,' TRPK '       , ' TPKE '     );
            when (index(adr,' PKY '))         adr=tranwrd(adr,' PKY '        , ' PKWY '     );
            when (index(adr,' FORGES '))      adr=tranwrd(adr,' FORGES '     , ' FRGS '     );
            when (index(adr,' BYPAS '))       adr=tranwrd(adr,' BYPAS '      , ' BYP '      );
            when (index(adr,' VIADUCT '))     adr=tranwrd(adr,' VIADUCT '    , ' VIA '      );
            when (index(adr,' MNT '))         adr=tranwrd(adr,' MNT '        , ' MT '       );
            when (index(adr,' LNDNG '))       adr=tranwrd(adr,' LNDNG '      , ' LNDG '     );
            when (index(adr,' VILL '))        adr=tranwrd(adr,' VILL '       , ' VLG '      );
            when (index(adr,' MILL '))        adr=tranwrd(adr,' MILL '       , ' ML '       );
            when (index(adr,' CENTERS '))     adr=tranwrd(adr,' CENTERS '    , ' CTRS '     );
            when (index(adr,' HRBOR '))       adr=tranwrd(adr,' HRBOR '      , ' HBR '      );
            when (index(adr,' TR '))          adr=tranwrd(adr,' TR '         , ' TRL '      );
            when (index(adr,' PASSAGE '))     adr=tranwrd(adr,' PASSAGE '    , ' PSGE '     );
            when (index(adr,' WALKS '))       adr=tranwrd(adr,' WALKS '      , ' WALK '     );
            when (index(adr,' CREST '))       adr=tranwrd(adr,' CREST '      , ' CRST '     );
            when (index(adr,' MEADOWS '))     adr=tranwrd(adr,' MEADOWS '    , ' MDWS '     );
            when (index(adr,' FREEWY '))      adr=tranwrd(adr,' FREEWY '     , ' FWY '      );
            when (index(adr,' GARDEN '))      adr=tranwrd(adr,' GARDEN '     , ' GDN '      );
            when (index(adr,' BLUFFS '))      adr=tranwrd(adr,' BLUFFS '     , ' BLFS '     );
            when (index(adr,' TRK '))         adr=tranwrd(adr,' TRK '        , ' TRAK '     );
            when (index(adr,' SQUARES '))     adr=tranwrd(adr,' SQUARES '    , ' SQS '      );
            when (index(adr,' HARBOR '))      adr=tranwrd(adr,' HARBOR '     , ' HBR '      );
            when (index(adr,' FRRY '))        adr=tranwrd(adr,' FRRY '       , ' FRY '      );
            when (index(adr,' DIV '))         adr=tranwrd(adr,' DIV '        , ' DV '       );
            when (index(adr,' STRAVEN '))     adr=tranwrd(adr,' STRAVEN '    , ' STRA '     );
            when (index(adr,' CMP '))         adr=tranwrd(adr,' CMP '        , ' CP '       );
            when (index(adr,' GRDNS '))       adr=tranwrd(adr,' GRDNS '      , ' GDNS '     );
            when (index(adr,' VILLG '))       adr=tranwrd(adr,' VILLG '      , ' VLG '      );
            when (index(adr,' MEADOW '))      adr=tranwrd(adr,' MEADOW '     , ' MDW '      );
            when (index(adr,' TRAILS '))      adr=tranwrd(adr,' TRAILS '     , ' TRL '      );
            when (index(adr,' STREETS '))     adr=tranwrd(adr,' STREETS '    , ' STS '      );
            when (index(adr,' PRAIRIE '))     adr=tranwrd(adr,' PRAIRIE '    , ' PR '       );
            when (index(adr,' CRESCENT '))    adr=tranwrd(adr,' CRESCENT '   , ' CRES '     );
            when (index(adr,' PORT '))        adr=tranwrd(adr,' PORT '       , ' PRT '      );
            when (index(adr,' TRAFFICWAY '))  adr=tranwrd(adr,' TRAFFICWAY ' , ' TRFY '     );
            when (index(adr,' BLUF '))        adr=tranwrd(adr,' BLUF '       , ' BLF '      );
            when (index(adr,' AVNUE '))       adr=tranwrd(adr,' AVNUE '      , ' AVE '      );
            when (index(adr,' LIGHTS '))      adr=tranwrd(adr,' LIGHTS '     , ' LGTS '     );
            when (index(adr,' HARBORS '))     adr=tranwrd(adr,' HARBORS '    , ' HBRS '     );
            when (index(adr,' LODG '))        adr=tranwrd(adr,' LODG '       , ' LDG '      );
            when (index(adr,' TRACKS '))      adr=tranwrd(adr,' TRACKS '     , ' TRAK '     );
            when (index(adr,' PKWAY '))       adr=tranwrd(adr,' PKWAY '      , ' PKWY '     );
            when (index(adr,' BOT '))         adr=tranwrd(adr,' BOT '        , ' BTM '      );
            when (index(adr,' DRV '))         adr=tranwrd(adr,' DRV '        , ' DR '       );
            when (index(adr,' DIVIDE '))      adr=tranwrd(adr,' DIVIDE '     , ' DV '       );
            when (index(adr,' FORDS '))       adr=tranwrd(adr,' FORDS '      , ' FRDS '     );
            when (index(adr,' AVENU '))       adr=tranwrd(adr,' AVENU '      , ' AVE '      );
            when (index(adr,' RIVR '))        adr=tranwrd(adr,' RIVR '       , ' RIV '      );
            when (index(adr,' GATEWAY '))     adr=tranwrd(adr,' GATEWAY '    , ' GTWY '     );
            when (index(adr,' STREAM '))      adr=tranwrd(adr,' STREAM '     , ' STRM '     );
            when (index(adr,' BAYOO '))       adr=tranwrd(adr,' BAYOO '      , ' BYU '      );
            when (index(adr,' KNOLL '))       adr=tranwrd(adr,' KNOLL '      , ' KNL '      );
            when (index(adr,' SPRNG '))       adr=tranwrd(adr,' SPRNG '      , ' SPG '      );
            when (index(adr,' FLAT '))        adr=tranwrd(adr,' FLAT '       , ' FLT '      );
            when (index(adr,' GRDEN '))       adr=tranwrd(adr,' GRDEN '      , ' GDN '      );
            when (index(adr,' TRAIL '))       adr=tranwrd(adr,' TRAIL '      , ' TRL '      );
            when (index(adr,' JCTNS '))       adr=tranwrd(adr,' JCTNS '      , ' JCTS '     );
            when (index(adr,' TUNNEL '))      adr=tranwrd(adr,' TUNNEL '     , ' TUNL '     );
            when (index(adr,' GROVES '))      adr=tranwrd(adr,' GROVES '     , ' GRVS '     );
            when (index(adr,' VALLY '))       adr=tranwrd(adr,' VALLY '      , ' VLY '      );
            when (index(adr,' FERRY '))       adr=tranwrd(adr,' FERRY '      , ' FRY '      );
            when (index(adr,' PARKWAY '))     adr=tranwrd(adr,' PARKWAY '    , ' PKWY '     );
            when (index(adr,' RADIEL '))      adr=tranwrd(adr,' RADIEL '     , ' RADL '     );
            when (index(adr,' STRVNUE '))     adr=tranwrd(adr,' STRVNUE '    , ' STRA '     );
            when (index(adr,' OVERPASS '))    adr=tranwrd(adr,' OVERPASS '   , ' OPAS '     );
            when (index(adr,' PLAZA '))       adr=tranwrd(adr,' PLAZA '      , ' PLZ '      );
            when (index(adr,' ESTATE '))      adr=tranwrd(adr,' ESTATE '     , ' EST '      );
            when (index(adr,' MNTN '))        adr=tranwrd(adr,' MNTN '       , ' MTN '      );
            when (index(adr,' LOCK '))        adr=tranwrd(adr,' LOCK '       , ' LCK '      );
            when (index(adr,' ORCHRD '))      adr=tranwrd(adr,' ORCHRD '     , ' ORCH '     );
            when (index(adr,' STRVN '))       adr=tranwrd(adr,' STRVN '      , ' STRA '     );
            when (index(adr,' LOCKS '))       adr=tranwrd(adr,' LOCKS '      , ' LCKS '     );
            when (index(adr,' BEND '))        adr=tranwrd(adr,' BEND '       , ' BND '      );
            when (index(adr,' JUNCTIONS '))   adr=tranwrd(adr,' JUNCTIONS '  , ' JCTS '     );
            when (index(adr,' MOUNTIN '))     adr=tranwrd(adr,' MOUNTIN '    , ' MTN '      );
            when (index(adr,' BURGS '))       adr=tranwrd(adr,' BURGS '      , ' BGS '      );
            when (index(adr,' PINE '))        adr=tranwrd(adr,' PINE '       , ' PNE '      );
            when (index(adr,' LDGE '))        adr=tranwrd(adr,' LDGE '       , ' LDG '      );
            when (index(adr,' CAUSWAY '))     adr=tranwrd(adr,' CAUSWAY '    , ' CSWY '     );
            when (index(adr,' BEACH '))       adr=tranwrd(adr,' BEACH '      , ' BCH '      );
            when (index(adr,' MOTORWAY '))    adr=tranwrd(adr,' MOTORWAY '   , ' MTWY '     );
            when (index(adr,' BLUFF '))       adr=tranwrd(adr,' BLUFF '      , ' BLF '      );
            when (index(adr,' COURT '))       adr=tranwrd(adr,' COURT '      , ' CT '       );
            when (index(adr,' GROV '))        adr=tranwrd(adr,' GROV '       , ' GRV '      );
            when (index(adr,' SPRNGS '))      adr=tranwrd(adr,' SPRNGS '     , ' SPGS '     );
            when (index(adr,' OVL '))         adr=tranwrd(adr,' OVL '        , ' OVAL '     );
            when (index(adr,' VILLAG '))      adr=tranwrd(adr,' VILLAG '     , ' VLG '      );
            when (index(adr,' VDCT '))        adr=tranwrd(adr,' VDCT '       , ' VIA '      );
            when (index(adr,' NECK '))        adr=tranwrd(adr,' NECK '       , ' NCK '      );
            when (index(adr,' ORCHARD '))     adr=tranwrd(adr,' ORCHARD '    , ' ORCH '     );
            when (index(adr,' LIGHT '))       adr=tranwrd(adr,' LIGHT '      , ' LGT '      );
            when (index(adr,' SHORE '))       adr=tranwrd(adr,' SHORE '      , ' SHR '      );
            when (index(adr,' GREEN '))       adr=tranwrd(adr,' GREEN '      , ' GRN '      );
            when (index(adr,' ISLND '))       adr=tranwrd(adr,' ISLND '      , ' IS '       );
            when (index(adr,' TURNPIKE '))    adr=tranwrd(adr,' TURNPIKE '   , ' TPKE '     );
            when (index(adr,' MISSION '))     adr=tranwrd(adr,' MISSION '    , ' MSN '      );
            when (index(adr,' SPNGS '))       adr=tranwrd(adr,' SPNGS '      , ' SPGS '     );
            when (index(adr,' COURSE '))      adr=tranwrd(adr,' COURSE '     , ' CRSE '     );
            when (index(adr,' TERRACE '))     adr=tranwrd(adr,' TERRACE '    , ' TER '      );
            when (index(adr,' HWAY '))        adr=tranwrd(adr,' HWAY '       , ' HWY '      );
            when (index(adr,' GLEN '))        adr=tranwrd(adr,' GLEN '       , ' GLN '      );
            when (index(adr,' BOUL '))        adr=tranwrd(adr,' BOUL '       , ' BLVD '     );
            when (index(adr,' INLET '))       adr=tranwrd(adr,' INLET '      , ' INLT '     );
            when (index(adr,' LA '))          adr=tranwrd(adr,' LA '         , ' LN '       );
            when (index(adr,' BROOK '))       adr=tranwrd(adr,' BROOK '      , ' BRK '      );
            when (index(adr,' SHOAR '))       adr=tranwrd(adr,' SHOAR '      , ' SHR '      );
            when (index(adr,' BYPASS '))      adr=tranwrd(adr,' BYPASS '     , ' BYP '      );
            when (index(adr,' MTIN '))        adr=tranwrd(adr,' MTIN '       , ' MTN '      );
            when (index(adr,' ALLY '))        adr=tranwrd(adr,' ALLY '       , ' ALY '      );
            when (index(adr,' FOREST '))      adr=tranwrd(adr,' FOREST '     , ' FRST '     );
            when (index(adr,' JUNCTION '))    adr=tranwrd(adr,' JUNCTION '   , ' JCT '      );
            when (index(adr,' VIEWS '))       adr=tranwrd(adr,' VIEWS '      , ' VWS '      );
            when (index(adr,' WELLS '))       adr=tranwrd(adr,' WELLS '      , ' WLS '      );
            when (index(adr,' CEN '))         adr=tranwrd(adr,' CEN '        , ' CTR '      );
            when (index(adr,' CRT '))         adr=tranwrd(adr,' CRT '        , ' CT '       );
            when (index(adr,' CORNERS '))     adr=tranwrd(adr,' CORNERS '    , ' CORS '     );
            when (index(adr,' FRWAY '))       adr=tranwrd(adr,' FRWAY '      , ' FWY '      );
            when (index(adr,' PRARIE '))      adr=tranwrd(adr,' PRARIE '     , ' PR '       );
            when (index(adr,' CROSSING '))    adr=tranwrd(adr,' CROSSING '   , ' XING '     );
            when (index(adr,' EXTN '))        adr=tranwrd(adr,' EXTN '       , ' EXT '      );
            when (index(adr,' CLIFFS '))      adr=tranwrd(adr,' CLIFFS '     , ' CLFS '     );
            when (index(adr,' MANORS '))      adr=tranwrd(adr,' MANORS '     , ' MNRS '     );
            when (index(adr,' PORTS '))       adr=tranwrd(adr,' PORTS '      , ' PRTS '     );
            when (index(adr,' GATEWY '))      adr=tranwrd(adr,' GATEWY '     , ' GTWY '     );
            when (index(adr,' SQUARE '))      adr=tranwrd(adr,' SQUARE '     , ' SQ '       );
            when (index(adr,' HARB '))        adr=tranwrd(adr,' HARB '       , ' HBR '      );
            when (index(adr,' LOOPS '))       adr=tranwrd(adr,' LOOPS '      , ' LOOP '     );
            when (index(adr,' HILL '))        adr=tranwrd(adr,' HILL '       , ' HL '       );
            when (index(adr,' HIGHWAY '))     adr=tranwrd(adr,' HIGHWAY '    , ' HWY '      );
            when (index(adr,' BROOKS '))      adr=tranwrd(adr,' BROOKS '     , ' BRKS '     );
            when (index(adr,' BRNCH '))       adr=tranwrd(adr,' BRNCH '      , ' BR '       );
            when (index(adr,' AVEN '))        adr=tranwrd(adr,' AVEN '       , ' AVE '      );
            when (index(adr,' SHORES '))      adr=tranwrd(adr,' SHORES '     , ' SHRS '     );
            when (index(adr,' PLACE '))       adr=tranwrd(adr,' PLACE '      , ' PL '       );
            when (index(adr,' SUMIT '))       adr=tranwrd(adr,' SUMIT '      , ' SMT '      );
            when (index(adr,' PINES '))       adr=tranwrd(adr,' PINES '      , ' PNES '     );
            when (index(adr,' TRKS '))        adr=tranwrd(adr,' TRKS '       , ' TRAK '     );
            when (index(adr,' SHOAL '))       adr=tranwrd(adr,' SHOAL '      , ' SHL '      );
            when (index(adr,' STRT '))        adr=tranwrd(adr,' STRT '       , ' ST '       );
            when (index(adr,' FRWY '))        adr=tranwrd(adr,' FRWY '       , ' FWY '      );
            when (index(adr,' RANCHES '))     adr=tranwrd(adr,' RANCHES '    , ' RNCH '     );
            when (index(adr,' BOULEVARD '))   adr=tranwrd(adr,' BOULEVARD '  , ' BLVD '     );
            when (index(adr,' EXTNSN '))      adr=tranwrd(adr,' EXTNSN '     , ' EXT '      );
            when (index(adr,' HOLLOWS '))     adr=tranwrd(adr,' HOLLOWS '    , ' HOLW '     );
            when (index(adr,' VSTA '))        adr=tranwrd(adr,' VSTA '       , ' VIS '      );
            when (index(adr,' PLAINS '))      adr=tranwrd(adr,' PLAINS '     , ' PLNS '     );
            when (index(adr,' STATION '))     adr=tranwrd(adr,' STATION '    , ' STA '      );
            when (index(adr,' MNTNS '))       adr=tranwrd(adr,' MNTNS '      , ' MTNS '     );
            when (index(adr,' VILLAGES '))    adr=tranwrd(adr,' VILLAGES '   , ' VLGS '     );
            when (index(adr,' HAVEN '))       adr=tranwrd(adr,' HAVEN '      , ' HVN '      );
            when (index(adr,' TURNPK '))      adr=tranwrd(adr,' TURNPK '     , ' TPKE '     );
            when (index(adr,' EXPR '))        adr=tranwrd(adr,' EXPR '       , ' EXPY '     );
            when (index(adr,' STN '))         adr=tranwrd(adr,' STN '        , ' STA '      );
            when (index(adr,' EXPW '))        adr=tranwrd(adr,' EXPW '       , ' EXPY '     );
            when (index(adr,' STR '))         adr=tranwrd(adr,' STR '        , ' ST '       );
            when (index(adr,' SPURS '))       adr=tranwrd(adr,' SPURS '      , ' SPUR '     );
            when (index(adr,' CRECENT '))     adr=tranwrd(adr,' CRECENT '    , ' CRES '     );
            when (index(adr,' RAD '))         adr=tranwrd(adr,' RAD '        , ' RADL '     );
            when (index(adr,' RANCH '))       adr=tranwrd(adr,' RANCH '      , ' RNCH '     );
            when (index(adr,' WELL '))        adr=tranwrd(adr,' WELL '       , ' WL '       );
            when (index(adr,' SHOALS '))      adr=tranwrd(adr,' SHOALS '     , ' SHLS '     );
            when (index(adr,' ALLEY '))       adr=tranwrd(adr,' ALLEY '      , ' ALY '      );
            when (index(adr,' PLZA '))        adr=tranwrd(adr,' PLZA '       , ' PLZ '      );
            when (index(adr,' MEDOWS '))      adr=tranwrd(adr,' MEDOWS '     , ' MDWS '     );
            when (index(adr,' ALLEE '))       adr=tranwrd(adr,' ALLEE '      , ' ALY '      );
            when (index(adr,' HAVN '))        adr=tranwrd(adr,' HAVN '       , ' HVN '      );
            when (index(adr,' PATHS '))       adr=tranwrd(adr,' PATHS '      , ' PATH '     );
            when (index(adr,' BYPA '))        adr=tranwrd(adr,' BYPA '       , ' BYP '      );
            when (index(adr,' MILLS '))       adr=tranwrd(adr,' MILLS '      , ' MLS '      );
            when (index(adr,' PARKS '))       adr=tranwrd(adr,' PARKS '      , ' PARK '     );
            when (index(adr,' BYPS '))        adr=tranwrd(adr,' BYPS '       , ' BYP '      );
            when (index(adr,' TUNNELS '))     adr=tranwrd(adr,' TUNNELS '    , ' TUNL '     );
            when (index(adr,' CLUB '))        adr=tranwrd(adr,' CLUB '       , ' CLB '      );
            when (index(adr,' SQRS '))        adr=tranwrd(adr,' SQRS '       , ' SQS '      );
            when (index(adr,' HLLW '))        adr=tranwrd(adr,' HLLW '       , ' HOLW '     );
            when (index(adr,' MANOR '))       adr=tranwrd(adr,' MANOR '      , ' MNR '      );
            when (index(adr,' CENTRE '))      adr=tranwrd(adr,' CENTRE '     , ' CTR '      );
            when (index(adr,' TRACK '))       adr=tranwrd(adr,' TRACK '      , ' TRAK '     );
            when (index(adr,' HGTS '))        adr=tranwrd(adr,' HGTS '       , ' HTS '      );
            when (index(adr,' CRCLE '))       adr=tranwrd(adr,' CRCLE '      , ' CIR '      );
            when (index(adr,' FALLS '))       adr=tranwrd(adr,' FALLS '      , ' FLS '      );
            when (index(adr,' LANDING '))     adr=tranwrd(adr,' LANDING '    , ' LNDG '     );
            when (index(adr,' PLAINES '))     adr=tranwrd(adr,' PLAINES '    , ' PLNS '     );
            when (index(adr,' VIADCT '))      adr=tranwrd(adr,' VIADCT '     , ' VIA '      );
            when (index(adr,' GROVE '))       adr=tranwrd(adr,' GROVE '      , ' GRV '      );
            when (index(adr,' CAMP '))        adr=tranwrd(adr,' CAMP '       , ' CP '       );
            when (index(adr,' TPK '))         adr=tranwrd(adr,' TPK '        , ' TPKE '     );
            when (index(adr,' DRIVE '))       adr=tranwrd(adr,' DRIVE '      , ' DR '       );
            when (index(adr,' FREEWAY '))     adr=tranwrd(adr,' FREEWAY '    , ' FWY '      );
            when (index(adr,' POINTS '))      adr=tranwrd(adr,' POINTS '     , ' PTS '      );
            when (index(adr,' EXP '))         adr=tranwrd(adr,' EXP '        , ' EXPY '     );
            when (index(adr,' COURTS '))      adr=tranwrd(adr,' COURTS '     , ' CTS '      );
            when (index(adr,' CORNER '))      adr=tranwrd(adr,' CORNER '     , ' COR '      );
            when (index(adr,' CRSSING '))     adr=tranwrd(adr,' CRSSING '    , ' XING '     );
            when (index(adr,' UNIONS '))      adr=tranwrd(adr,' UNIONS '     , ' UNS '      );
            when (index(adr,' LODGE '))       adr=tranwrd(adr,' LODGE '      , ' LDG '      );
            when (index(adr,' CIRCLE '))      adr=tranwrd(adr,' CIRCLE '     , ' CIR '      );
            when (index(adr,' BRIDGE '))      adr=tranwrd(adr,' BRIDGE '     , ' BRG '      );
            when (index(adr,' EXPRESS '))     adr=tranwrd(adr,' EXPRESS '    , ' EXPY '     );
            when (index(adr,' TUNLS '))       adr=tranwrd(adr,' TUNLS '      , ' TUNL '     );
            when (index(adr,' KNOLLS '))      adr=tranwrd(adr,' KNOLLS '     , ' KNLS '     );
            when (index(adr,' GREENS '))      adr=tranwrd(adr,' GREENS '     , ' GRNS '     );
            when (index(adr,' TUNEL '))       adr=tranwrd(adr,' TUNEL '      , ' TUNL '     );
            when (index(adr,' FIELDS '))      adr=tranwrd(adr,' FIELDS '     , ' FLDS '     );
            when (index(adr,' COMMON '))      adr=tranwrd(adr,' COMMON '     , ' CMN '      );
            when (index(adr,' RIVER '))       adr=tranwrd(adr,' RIVER '      , ' RIV '      );
            when (index(adr,' VIEW '))        adr=tranwrd(adr,' VIEW '       , ' VW '       );
            when (index(adr,' CRSENT '))      adr=tranwrd(adr,' CRSENT '     , ' CRES '     );
            when (index(adr,' RNCHS '))       adr=tranwrd(adr,' RNCHS '      , ' RNCH '     );
            when (index(adr,' CRSCNT '))      adr=tranwrd(adr,' CRSCNT '     , ' CRES '     );
            when (index(adr,' RDGE '))        adr=tranwrd(adr,' RDGE '       , ' RDG '      );
            when (index(adr,' CAUSEWAY '))    adr=tranwrd(adr,' CAUSEWAY '   , ' CSWY '     );
            when (index(adr,' PARKWY '))      adr=tranwrd(adr,' PARKWY '     , ' PKWY '     );
            when (index(adr,' JUNCTON '))     adr=tranwrd(adr,' JUNCTON '    , ' JCT '      );
            when (index(adr,' STATN '))       adr=tranwrd(adr,' STATN '      , ' STA '      );
            when (index(adr,' GARDN '))       adr=tranwrd(adr,' GARDN '      , ' GDN '      );
            when (index(adr,' MNTAIN '))      adr=tranwrd(adr,' MNTAIN '     , ' MTN '      );
            when (index(adr,' CRSSNG '))      adr=tranwrd(adr,' CRSSNG '     , ' XING '     );
            when (index(adr,' RAPID '))       adr=tranwrd(adr,' RAPID '      , ' RPD '      );
            when (index(adr,' KEY '))         adr=tranwrd(adr,' KEY '        , ' KY '       );
            when (index(adr,' WY '))          adr=tranwrd(adr,' WY '         , ' WAY '      );
            when (index(adr,' THROUGHWAY '))  adr=tranwrd(adr,' THROUGHWAY ' , ' TRWY '     );
            when (index(adr,' ESTATES '))     adr=tranwrd(adr,' ESTATES '    , ' ESTS '     );
            when (index(adr,' CK '))          adr=tranwrd(adr,' CK '         , ' CRK '      );
            when (index(adr,' LOAF '))        adr=tranwrd(adr,' LOAF '       , ' LF '       );
            when (index(adr,' HOLLOW '))      adr=tranwrd(adr,' HOLLOW '     , ' HOLW '     );
            when (index(adr,' CANYON '))      adr=tranwrd(adr,' CANYON '     , ' CYN '      );
            when (index(adr,' VILLAGE '))     adr=tranwrd(adr,' VILLAGE '    , ' VLG '      );
            when (index(adr,' CR '))          adr=tranwrd(adr,' CR '         , ' CRK '      );
            when (index(adr,' CT '))          adr=tranwrd(adr,' CT '         , ' CTS '      );
            when (index(adr,' JCTION '))      adr=tranwrd(adr,' JCTION '     , ' JCT '      );
            when (index(adr,' MSSN '))        adr=tranwrd(adr,' MSSN '       , ' MSN '      );
            when (index(adr,' BRDGE '))       adr=tranwrd(adr,' BRDGE '      , ' BRG '      );
            when (index(adr,' CENT '))        adr=tranwrd(adr,' CENT '       , ' CTR '      );
            when (index(adr,' FRT '))         adr=tranwrd(adr,' FRT '        , ' FT '       );
            when (index(adr,' PK '))          adr=tranwrd(adr,' PK '         , ' PARK '     );
            when (index(adr,' LANES '))       adr=tranwrd(adr,' LANES '      , ' LN '       );
            when (index(adr,' GTWAY '))       adr=tranwrd(adr,' GTWAY '      , ' GTWY '     );
            when (index(adr,' PRK '))         adr=tranwrd(adr,' PRK '        , ' PARK '     );
            when (index(adr,' STRAVENUE '))   adr=tranwrd(adr,' STRAVENUE '  , ' STRA '     );
            when (index(adr,' HIWAY '))       adr=tranwrd(adr,' HIWAY '      , ' HWY '      );
            when (index(adr,' VILLE '))       adr=tranwrd(adr,' VILLE '      , ' VL '       );
            when (index(adr,' PLAIN '))       adr=tranwrd(adr,' PLAIN '      , ' PLN '      );
            when (index(adr,' MOUNT '))       adr=tranwrd(adr,' MOUNT '      , ' MT '       );
            when (index(adr,' CENTR '))       adr=tranwrd(adr,' CENTR '      , ' CTR '      );
            when (index(adr,' PRR '))         adr=tranwrd(adr,' PRR '        , ' PR '       );
            when (index(adr,' AVN '))         adr=tranwrd(adr,' AVN '        , ' AVE '      );
            when (index(adr,' SPNG '))        adr=tranwrd(adr,' SPNG '       , ' SPG '      );
            when (index(adr,' HIWY '))        adr=tranwrd(adr,' HIWY '       , ' HWY '      );
            when (index(adr,' DAM '))         adr=tranwrd(adr,' DAM '        , ' DM '       );
            when (index(adr,' CRCL '))        adr=tranwrd(adr,' CRCL '       , ' CIR '      );
            when (index(adr,' SQRE '))        adr=tranwrd(adr,' SQRE '       , ' SQ '       );
            when (index(adr,' JCTN '))        adr=tranwrd(adr,' JCTN '       , ' JCT '      );
            when (index(adr,' MOUNTAIN '))    adr=tranwrd(adr,' MOUNTAIN '   , ' MTN '      );
            when (index(adr,' KEYS '))        adr=tranwrd(adr,' KEYS '       , ' KYS '      );
            when (index(adr,' PARKWAYS '))    adr=tranwrd(adr,' PARKWAYS '   , ' PKWY '     );
            when (index(adr,' DRIVES '))      adr=tranwrd(adr,' DRIVES '     , ' DRS '      );
            when (index(adr,' CENTER '))      adr=tranwrd(adr,' CENTER '     , ' CTR '      );
            when (index(adr,' DRIV '))        adr=tranwrd(adr,' DRIV '       , ' DR '       );
            when (index(adr,' SUMITT '))      adr=tranwrd(adr,' SUMITT '     , ' SMT '      );
            when (index(adr,' CANYN '))       adr=tranwrd(adr,' CANYN '      , ' CYN '      );
            when (index(adr,' HARBR '))       adr=tranwrd(adr,' HARBR '      , ' HBR '      );
            when (index(adr,' REST '))        adr=tranwrd(adr,' REST '       , ' RST '      );
            when (index(adr,' SHOARS '))      adr=tranwrd(adr,' SHOARS '     , ' SHRS '     );
            when (index(adr,' VIST '))        adr=tranwrd(adr,' VIST '       , ' VIS '      );
            when (index(adr,' ISLNDS '))      adr=tranwrd(adr,' ISLNDS '     , ' ISS '      );
            when (index(adr,' HILLS '))       adr=tranwrd(adr,' HILLS '      , ' HLS '      );
            when (index(adr,' CRESENT '))     adr=tranwrd(adr,' CRESENT '    , ' CRES '     );
            when (index(adr,' POINT '))       adr=tranwrd(adr,' POINT '      , ' PT '       );
            when (index(adr,' LAKE '))        adr=tranwrd(adr,' LAKE '       , ' LK '       );
            when (index(adr,' VLLY '))        adr=tranwrd(adr,' VLLY '       , ' VLY '      );
            when (index(adr,' STRAV '))       adr=tranwrd(adr,' STRAV '      , ' STRA '     );
            when (index(adr,' CROSSROAD '))   adr=tranwrd(adr,' CROSSROAD '  , ' XRD '      );
            when (index(adr,' STRAVE '))      adr=tranwrd(adr,' STRAVE '     , ' STRA '     );
            when (index(adr,' STRAVN '))      adr=tranwrd(adr,' STRAVN '     , ' STRA '     );
            when (index(adr,' KNOL '))        adr=tranwrd(adr,' KNOL '       , ' KNL '      );
            when (index(adr,' FORGE '))       adr=tranwrd(adr,' FORGE '      , ' FRG '      );
            when (index(adr,' CNTR '))        adr=tranwrd(adr,' CNTR '       , ' CTR '      );
            when (index(adr,' CAPE '))        adr=tranwrd(adr,' CAPE '       , ' CPE '      );
            when (index(adr,' HEIGHT '))      adr=tranwrd(adr,' HEIGHT '     , ' HTS '      );
            when (index(adr,' HIGHWY '))      adr=tranwrd(adr,' HIGHWY '     , ' HWY '      );
            when (index(adr,' TRNPK '))       adr=tranwrd(adr,' TRNPK '      , ' TPKE '     );
            when (index(adr,' BOULV '))       adr=tranwrd(adr,' BOULV '      , ' BLVD '     );
            when (index(adr,' CIRCLES '))     adr=tranwrd(adr,' CIRCLES '    , ' CIRS '     );
            when (index(adr,' VALLEYS '))     adr=tranwrd(adr,' VALLEYS '    , ' VLYS '     );
            when (index(adr,' VST '))         adr=tranwrd(adr,' VST '        , ' VIS '      );
            when (index(adr,' SPRING '))      adr=tranwrd(adr,' SPRING '     , ' SPG '      );
            when (index(adr,' HOLWS '))       adr=tranwrd(adr,' HOLWS '      , ' HOLW '     );
            when (index(adr,' TRACE '))       adr=tranwrd(adr,' TRACE '      , ' TRCE '     );
            when (index(adr,' BOTTOM '))      adr=tranwrd(adr,' BOTTOM '     , ' BTM '      );
            when (index(adr,' STREME '))      adr=tranwrd(adr,' STREME '     , ' STRM '     );
            when (index(adr,' ISLES '))       adr=tranwrd(adr,' ISLES '      , ' ISLE '     );
            when (index(adr,' CIRC '))        adr=tranwrd(adr,' CIRC '       , ' CIR '      );
            when (index(adr,' FORKS '))       adr=tranwrd(adr,' FORKS '      , ' FRKS '     );
            when (index(adr,' BURG '))        adr=tranwrd(adr,' BURG '       , ' BG '       );
            when (index(adr,' TRLS '))        adr=tranwrd(adr,' TRLS '       , ' TRL '      );
            when (index(adr,' RADIAL '))      adr=tranwrd(adr,' RADIAL '     , ' RADL '     );
            when (index(adr,' LAKES '))       adr=tranwrd(adr,' LAKES '      , ' LKS '      );
            when (index(adr,' EXTENSION '))   adr=tranwrd(adr,' EXTENSION '  , ' EXT '      );
            when (index(adr,' ISLAND '))      adr=tranwrd(adr,' ISLAND '     , ' IS '       );
            when (index(adr,' TERR '))        adr=tranwrd(adr,' TERR '       , ' TER '      );
            when (index(adr,' UNION '))       adr=tranwrd(adr,' UNION '      , ' UN '       );
            when (index(adr,' EXTENSIONS '))  adr=tranwrd(adr,' EXTENSIONS ' , ' EXTS '     );
            when (index(adr,' PKWYS '))       adr=tranwrd(adr,' PKWYS '      , ' PKWY '     );
            when (index(adr,' ISLANDS '))     adr=tranwrd(adr,' ISLANDS '    , ' ISS '      );
            when (index(adr,' ROADS '))       adr=tranwrd(adr,' ROADS '      , ' RDS '      );
            when (index(adr,' GLENS '))       adr=tranwrd(adr,' GLENS '      , ' GLNS '     );
            when (index(adr,' SPRINGS '))     adr=tranwrd(adr,' SPRINGS '    , ' SPGS '     );
            when (index(adr,' MISSN '))       adr=tranwrd(adr,' MISSN '      , ' MSN '      );
            when (index(adr,' RIDGE '))       adr=tranwrd(adr,' RIDGE '      , ' RDG '      );
            when (index(adr,' ARCADE '))      adr=tranwrd(adr,' ARCADE '     , ' ARC '      );
            when (index(adr,' BAYOU '))       adr=tranwrd(adr,' BAYOU '      , ' BYU '      );
            when (index(adr,' CRSNT '))       adr=tranwrd(adr,' CRSNT '      , ' CRES '     );
            when (index(adr,' JUNCTN '))      adr=tranwrd(adr,' JUNCTN '     , ' JCT '      );
            when (index(adr,' FORK '))        adr=tranwrd(adr,' FORK '       , ' FRK '      );
            when (index(adr,' MOUNTAINS '))   adr=tranwrd(adr,' MOUNTAINS '  , ' MTNS '     );
            when (index(adr,' BOTTM '))       adr=tranwrd(adr,' BOTTM '      , ' BTM '      );
            when (index(adr,' FORG '))        adr=tranwrd(adr,' FORG '       , ' FRG '      );
            when (index(adr,' HT '))          adr=tranwrd(adr,' HT '         , ' HTS '      );
            when (index(adr,' FORD '))        adr=tranwrd(adr,' FORD '       , ' FRD '      );
            when (index(adr,' GRDN '))        adr=tranwrd(adr,' GRDN '       , ' GDN '      );
            when (index(adr,' FORT '))        adr=tranwrd(adr,' FORT '       , ' FT '       );
            when (index(adr,' TRACES '))      adr=tranwrd(adr,' TRACES '     , ' TRCE '     );
            when (index(adr,' CNYN '))        adr=tranwrd(adr,' CNYN '       , ' CYN '      );
            when (index(adr,' FLATS '))       adr=tranwrd(adr,' FLATS '      , ' FLTS '     );
            when (index(adr,' ANEX '))        adr=tranwrd(adr,' ANEX '       , ' ANX '      );
            when (index(adr,' GATWAY '))      adr=tranwrd(adr,' GATWAY '     , ' GTWY '     );
            when (index(adr,' RAPIDS '))      adr=tranwrd(adr,' RAPIDS '     , ' RPDS '     );
            when (index(adr,' VILLIAGE '))    adr=tranwrd(adr,' VILLIAGE '   , ' VLG '      );
            when (index(adr,' COVES '))       adr=tranwrd(adr,' COVES '      , ' CVS '      );
            when (index(adr,' RVR '))         adr=tranwrd(adr,' RVR '        , ' RIV '      );
            when (index(adr,' AV '))          adr=tranwrd(adr,' AV '         , ' AVE '      );
            when (index(adr,' PIKES '))       adr=tranwrd(adr,' PIKES '      , ' PIKE '     );
            when (index(adr,' VISTA '))       adr=tranwrd(adr,' VISTA '      , ' VIS '      );
            when (index(adr,' FORESTS '))     adr=tranwrd(adr,' FORESTS '    , ' FRST '     );
            when (index(adr,' FIELD '))       adr=tranwrd(adr,' FIELD '      , ' FLD '      );
            when (index(adr,' BRANCH '))      adr=tranwrd(adr,' BRANCH '     , ' BR '       );
            when (index(adr,' DALE '))        adr=tranwrd(adr,' DALE '       , ' DL '       );
            when (index(adr,' ANNEX '))       adr=tranwrd(adr,' ANNEX '      , ' ANX '      );
            when (index(adr,' SQR '))         adr=tranwrd(adr,' SQR '        , ' SQ '       );
            when (index(adr,' COVE '))        adr=tranwrd(adr,' COVE '       , ' CV '       );
            when (index(adr,' SQU '))         adr=tranwrd(adr,' SQU '        , ' SQ '       );
            when (index(adr,' SKYWAY '))      adr=tranwrd(adr,' SKYWAY '     , ' SKWY '     );
            when (index(adr,' RIDGES '))      adr=tranwrd(adr,' RIDGES '     , ' RDGS '     );
            when (index(adr,' TUNNL '))       adr=tranwrd(adr,' TUNNL '      , ' TUNL '     );
            when (index(adr,' UNDERPASS '))   adr=tranwrd(adr,' UNDERPASS '  , ' UPAS '     );
            when (index(adr,' CLIFF '))       adr=tranwrd(adr,' CLIFF '      , ' CLF '      );
            when (index(adr,' LANE '))        adr=tranwrd(adr,' LANE '       , ' LN '       );
            when (index(adr,' DVD '))         adr=tranwrd(adr,' DVD '        , ' DV '       );
            when (index(adr,' CURVE '))       adr=tranwrd(adr,' CURVE '      , ' CURV '     );
            when (index(adr,' SUMMIT '))      adr=tranwrd(adr,' SUMMIT '     , ' SMT '      );
            when (index(adr,' GARDENS '))     adr=tranwrd(adr,' GARDENS '    , ' GDNS '     );
            otherwise;
         end;
    ;;;;
    run;quit;

    /*---- from my autoexec                                                  ----*/

    %let states50q="AL","AK","AZ","AR","CA","CO","CT","DE","FL","GA","HI","ID","IL","IN","IA","KS","KY","LA","ME","MD","MA","MI","MN","MS","MO","MT",
    "NE","NV","NH","NJ","NM","NY","NC","ND","OH","OK","OR","PA","RI","SC","SD","TN","TX","UT","VT","VA","WA","WV","WI","WY";

    /*                                               _ _
     _ __ ___ _ __ ___   _____   _____   _   _ _ __ (_) |_
    | `__/ _ \ `_ ` _ \ / _ \ \ / / _ \ | | | | `_ \| | __|
    | | |  __/ | | | | | (_) \ V /  __/ | |_| | | | | | |_
    |_|  \___|_| |_| |_|\___/ \_/ \___|  \__,_|_| |_|_|\__|

    */

    /*----                                                                   ----*/
    /*----  remove internal strings STE XX UNIT XX APT XX (same lat lon)     ----*/
    /*----  One address for a building                                       ----*/
    /*----                                                                   ----*/

    %macro unit(unit);

      if index(adr," &unit ") then do;
         *put adr=;
         idx=index(adr," &unit " );
         nxt=scan(substr(adr,idx),2);
         *put nxt=;
         wrdlen=length(catx(" ","&unit" ,strip(nxt)))
    ;    *put wrdlen=;
         substr(adr,idx+1,wrdlen)="";
         *put adr=;
         adr=compbl(adr);
         *put adr= // ;
         flg="UN";
      end;

    %mend unit;

    /*__ _             _       _                  _               _
     / _(_)_ __   __ _| |  ___| |_ __ _ _ __   __| | __ _ _ __ __| |
    | |_| | `_ \ / _` | | / __| __/ _` | `_ \ / _` |/ _` | `__/ _` |
    |  _| | | | | (_| | | \__ \ || (_| | | | | (_| | (_| | | | (_| |
    |_| |_|_| |_|\__,_|_| |___/\__\__,_|_| |_|\__,_|\__,_|_|  \__,_|

    */


    data adr_010bstMat;

        retain
           ZIP4
           MATCHCODE
           STATE
           ADR
        ;

        length zip4 $4;

        set  adr.adr_010smpadr( rename=bst_street=adr);

        if index(adr,"PO BOX") then delete;  * do not handle PO BOX yet *;

        if bst_state in (&states50q);  * only us states *;

        if countw(adr) > 2 then do;
           len=length(strip(scan(adr,-1,' ')));
           sfx=strip(scan(adr,-1,' '));
           select;
               %include "d:/adr/sas/adr_010sfx.sas";   * ending sfx *;
              otherwise;
           end;
        end;

        adr=catx(' ',cats(compress(adr,,'kads')),bst_zip5); * add zip5 *;

        %inc "d:/adr/sas/adr_010sx1.sas";  * internal adrv *;

        adr=tranwrd(adr,'NORTH','N');      * better for matchcode? *;
        adr=tranwrd(adr,'SOUTH','S');
        adr=tranwrd(adr,'EAST','E');
        adr=tranwrd(adr,'WEST','W');
        adr=tranwrd(adr,'NORTHEAST','NE');
        adr=tranwrd(adr,'SOUTHEAST','SE');
        adr=tranwrd(adr,'NORTHWEST','NW');
        adr=tranwrd(adr,'SOUTHWEST','SW');

        %unit(APT);  * remove APT # UNIT # and STE # - same buliding *;
        %unit(UNIT);
        %unit(STE);

        matchcode = compress(adr,' ');

        zip4=substr(bst_zip5,1,4);

        state=bst_state;

        keep
           MATCHCODE
           ZIP4
           STATE
           ADR
        ;

    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*   WORK.ADR_010BSTMAT total obs=37                                                                                      */
    /*                                                                                                                        */
    /*   ZIP4    MATCHCODE                   STATE    ADR                                                                     */
    /*                                                                                                                        */
    /*   5240    5614WHEATLANDSWDR52404       IA      5614 WHEATLAND SW DR 52404                                              */
    /*   5150    800MERCYDR51503              IA      800 MERCY DR 51503                                                      */
    /*   5110    8015THST51101                IA      801 5TH ST 51101                                                        */
    /*   5030    1215PLEASANTST50309          IA      1215 PLEASANT ST 50309                                                  */
    /*   5257    1229CAVEE52577               IA      1229 C AVE E 52577                                                      */
    /*   5224    540EJEFFERSONST52245         IA      540 E JEFFERSON ST 52245                                                */
    /*   5240    1026AAVENE52406              IA      1026 A AVE NE 52406                                                     */
    /*   5031    3509E29THST50317             IA      3509 E 29TH ST 50317                                                    */
    /*   5280    1820E54THST52807             IA      1820 E 54TH ST 52807                                                    */
    /*   6153    102NMYERS61530               IL      102 N MYERS 61530                                                       */
    /*   6002    1201HARMSRD60025             IL      1201 HARMS RD 60025                                                     */
    /*   6186    1421PNEAVE61866              IL      1421 PNE AVE 61866                                                      */
    /*   6065    1712WBELMONTAVE60657         IL      1712 W BELMONT AVE 60657                                                */
    /*   6052    202BRIDLEPATHCIR60523        IL      202 BRIDLE PATH CIR 60523                                               */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    proc sort data=adr_010bstMat out=adr_010bstMatSrt; * spped up matching - locality of references *;
      by zip4 matchcode state adr;
    run;quit;

    proc sql;
       create
          table adr_010matScr as
       select
          fro_adr
         ,too_adr
         ,r.avglon
         ,r.avglat
         ,min(score)  as min_score
       from
          (
          select
             l.adr                   as fro_adr
            ,r.adr                   as too_adr
            ,compged(compress(l.matchcode),compress(r.matchcode)) as score
          from
             adr_010bstMatSrt as l left join adr.adr_010adrLonLatSrt as r
          on
                  compged(l.matchcode,r.matchcode) le 500
             and  l.zip4 = r.zip4
          )
      group
         by fro_adr
      having
         score = min(score)
      order
         by fro_adr, score
    ;quit;

    data adr_010matAdr;
      set adr_010matScr;
      by fro_adr;
      if first.fro_adr;
    run;quit;

    proc sort data=adr_010matAdr out=adr_010matFin;
    by min_score;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  WORK.ADR_010MATFIN total obs=37                                                                                       */
    /*                                                                                                                        */
    /*   MESSY                                                                                  MIN_                          */
    /*   FRO_ADR                          TOO_ADR                        AVGLON     AVGLAT     SCORE                          */
    /*                                                                                                                        */
    /*   1025 S 6TH ST 62703              1025 S 6TH ST 62703           39.7911    -89.6485        0                          */
    /*   1201 HARMS RD 60025              1201 HARMS RD 60025           42.0765    -87.7708        0                          */
    /*   1215 PLEASANT ST 50309           1215 PLEASANT ST 50309        41.5897    -93.6351        0                          */
    /*   1229 C AVE E 52577               1229 C AVE E 52577            41.2988    -92.6307        0                          */
    /*   1421 PNE AVE 61866               1421 PNE AVE 61866            40.3153    -88.1355        0                          */
    /*   1712 W BELMONT AVE 60657         1712 W BELMONT AVE 60657      41.9399    -87.6717        0                          */
    /*   1820 E 54TH ST 52807             1820 E 54TH ST 52807          41.5770    -90.5496        0                          */
    /*   1870 W GALENA BLVD 60506         1870 W GALENA BLVD 60506      41.7625    -88.3623        0                          */
    /*   1900 W POLK ST 60612             1900 W POLK ST 60612          41.8719    -87.6734        0                          */
    /*   202 BRIDLE PATH CIR 60523        202 BRIDLE PATH CIR 60523     41.8203    -87.9821        0                          */
    /*   225 E CHICAGO AVE 60611          225 E CHICAGO AVE 60611       41.8965    -87.6218        0                          */
    /*   2520 ELISHA AVE 60099            2520 ELISHA AVE 60099         42.4494    -87.8280        0                          */
    /*   2550 CHARLES ST 61108            2550 CHARLES ST 61108         42.2607    -89.0558        0                          */
    /*   2906 W WILSON AVE 60625          2906 W WILSON AVE 60625       41.9650    -87.7014        0                          */
    /*   3509 E 29TH ST 50317             3509 E 29TH ST 50317          41.6269    -93.5607        0                          */
    /*   515 STONE RDG LN 61016           515 STONE RDG LN 61016        42.2533    -88.9320        0                          */
    /*   540 E JEFFERSON ST 52245         540 E JEFFERSON ST 52245      41.6633    -91.5279        0                          */
    /*   6201 WILDWOOD LN 60527           6201 WILDWOOD LN 60527        41.7764    -87.9331        0                          */
    /*   680 N LAKE SHR DR 60611          680 N LAKE SHR DR 60611       41.8948    -87.6167        0                          */
    /*   71 W 156TH ST 60426              71 W 156TH ST 60426           41.6067    -87.6601        0                          */
    /*   738 N WASHINGTON AVE 60068       738 N WASHINGTON AVE 60068    42.0214    -87.8264        0                          */
    /*   800 MERCY DR 51503               800 MERCY DR 51503            41.2687    -95.8388        0                          */
    /*   800 W CENTRAL RD 60005           800 W CENTRAL RD 60005        42.0682    -87.9925        0                          */
    /*   801 5TH ST 51101                 801 5TH ST 51101              42.4956    -96.4007        0                          */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*   SMALL Differnces                                                                                                     */
    /*                                                                                                                        */
    /*   1026 A AVE NE 52406              1026 A AVE NE 52402           41.9853    -91.6606      100                          */
    /*   4230 LINCOLNSHIRE DR G 62864     4230 LINCOLNSHIRE DR 62864    38.3204    -88.9446      100                          */
    /*   5841 S MARYLAND AVE 60637        5811 S MARYLAND AVE 60637     41.7891    -87.6046      100                          */
    /*   901 BEAR CRK CTS 62232           901 BEAR CRK CT 62232         38.6332    -89.9717      100                          */
    /*   102 N MYERS 61530                102 N MYERS ST 61530          40.7216    -89.2756      120                          */
    /*   346 WILSHIRE E DR 60091          346 WILSHIRE DR E 60091       42.0699    -87.7379      120                          */
    /*   235 A S MAIN 62025               235A S MAIN ST 62025          38.8092    -89.9554      200                          */
    /*   2800 W 95TH ST 3N 60805          2800 W 95TH ST 60805          41.7215    -87.6928      200                          */
    /*   836 W WELLINGTON 60657           836 W WELLINGTON AVE 60657    41.9370    -87.6512      300                          */
    /*   5614 WHEATLAND SW DR 52404       5614 WHEATLAND DR SW 52404    41.9223    -91.6336      320                          */
    /*   1653 W CONGRESS BLVD 60612       1753 W CONGRESS PKWY 60612    41.8747    -87.6712      500                          */
    /*   701 N 1ST ST 62781                                               .           .         2800                          */
    /*   2160 S 1ST AVE BLDG 101 60153 =>fix in future (remove BLDG 101)  .           .         4600                          */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
