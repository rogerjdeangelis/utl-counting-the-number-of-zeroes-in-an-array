# utl-counting-the-number-of-zeroes-in-an-array
    Counting the number of zeroes in an array

    This is purely and academic exercise.

    I would not use these two solutions in practive.

    Two solutions

     1. peekclong (issues - array max length 32k and pagesize over 32k? - elements
               of permanent arrays are not always contiguous)

      2. catx defaults to convert single integers to character (only works with single integers
               and has the linits above)

      3. Production solution by  Bartosz Jablonski <yabwon@GMAIL.COM>

    Barts Fcmp Solution
    options cmplib=_null_;

    proc fcmp outlib=work.f.p;
      function counter(z, a[*]) VARARGS;
        length x 8;
        x = 0;
        do i = 1 to dim(a);
          x = x + (z = a[i]);
        end;

        return (x);
      endsub;
    run;

    options cmplib=work.f;

    data have;

      array m(6);
      input m(*);

      count0 = counter(0, m);

    cards4;
    0 4 3 7 6 8
    3 7 5 0 5 0
    6 8 6 2 8 0
    4 7 0 7 9 4
    5 8 6 0 5 7
    ;;;;
    run;quit;

    Educational Solutions

    These methods have been posted by others. I don't have the authors
    names.
    Related to
    SAS Forum
    https://tinyurl.com/ycf6l29w
    https://communities.sas.com/t5/SAS-Procedures/Counting-gt-0-in-observation-across-variables/m-p/528142
    Novinosrin profile
    https://communities.sas.com/t5/user/viewprofilepage/user-id/138205
    INPUT
    =====
    data have;
      array m(6);
      input m(*);
    cards4;
    0 4 3 7 6 8
    3 7 5 0 5 0
    6 8 6 2 8 0
    4 7 0 7 9 4
    5 8 6 0 5 7
    ;;;;
    run;quit;
    /*
     WORK.HAVE total obs=5               | RULES
                                         |
      M1    M2    M3    M4    M5    M6   |  ZEROES
                                         |
       0     4     3     7     6     8   |   1
       3     7     5     0     5     0   |   2
       6     8     6     2     8     0   |   1
       4     7     0     7     9     4   |   1
       5     8     6     0     5     7   |   1
    */
    PROCESS
    ========
    ------------
    1. PeekClong
    ------------
    data wantPeek;
      set have;
      array m(6);
      zeroes=count(peekclong(addrlong(m[1]),48),put(0,rb8.));
    run;quit;
    /*
    WORK.WANTPEEK total obs=5
       M1    M2    M3    M4    M5    M6    ZEROES
        0     4     3     7     6     8       1
        3     7     5     0     5     0       2
        6     8     6     2     8     0       1
        4     7     0     7     9     4       1
        5     8     6     0     5     7       1
    */
    =======
    2. Catx
    =======
    data wantcatx;
      set have;
      array t(*) m1--m6;/**group your months vars with a double dash*/
      k=compress(catx(',',of t(*))); /* default behavior of catx is convert integer to character */
      want=count(k,'0');
    drop k;
    run;
    /*
    WORK.WANTCATX total obs=5
       M1    M2    M3    M4    M5    M6    ZEROES
        0     4     3     7     6     8       1
        3     7     5     0     5     0       2
        6     8     6     2     8     0       1
        4     7     0     7     9     4       1
        5     8     6     0     5     7       1
    */



