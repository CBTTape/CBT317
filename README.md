# CBT317
Converted to GitHub via [cbt2git](https://github.com/wizardofzos/cbt2git)

This is still a work in progress. 
Due to amazing work by Alison Zhang and Jake Choi repos are no longer deleted.

```
//***FILE 317 IS FROM TIM HENNESS OF TENNECO BUSINESS SERVICES IN   *   FILE 317
//*           NEWPORT NEWS, VIRGINIA, AND CONTAINS A REXX EXEC TO   *   FILE 317
//*           MANAGE IBM BOOK MANAGER BOOKS.                        *   FILE 317
//*                                                                 *   FILE 317
//*           THIS REXX EXEC IS BEST RUN UNDER TSO-IN-BATCH.        *   FILE 317
//*           SAMPLE JCL IS INCLUDED IN THIS FILE.                  *   FILE 317
//*                                                                 *   FILE 317
//*       EMAIL:   TimHenness@ibm.net                               *   FILE 317
//*                                                                 *   FILE 317
//*  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -  *   FILE 317
//*                                                                 *   FILE 317
//*  MAINTAIN BOOKMANAGER DATA SETS                                 *   FILE 317
//*                                                                 *   FILE 317
//*  PROGRAM NAME - BKMGRMNT                                        *   FILE 317
//*                                                                 *   FILE 317
//*  FUNCTION -                                                     *   FILE 317
//*                                                                 *   FILE 317
//*     THIS REXX EXEC WILL INDICATE THE MAINTAINANCE REQUIRED      *   FILE 317
//*     FOR BOOKMANAGER DATA SETS.  THE FOLLOWING LISTINGS ARE      *   FILE 317
//*     PRODUCED:                                                   *   FILE 317
//*                                                                 *   FILE 317
//*     1.  LIST OF BOOKSHELVES NOT IN THE BOOKSHELF LIST.          *   FILE 317
//*                                                                 *   FILE 317
//*     2.  LIST OF BOOKSHELF INDEXES NOT USED BY ANY BOOKSHELF.    *   FILE 317
//*                                                                 *   FILE 317
//*     3.  LIST OF BOOKS NOT IN ANY BOOKSHELF.                     *   FILE 317
//*                                                                 *   FILE 317
//*     4.  LIST OF BOOKS TO BE REPLACED IN SPECIAL BOOKSHELVES.    *   FILE 317
//*                                                                 *   FILE 317
//*     ALSO, A REXX EXEC CAN BE WRITTEN THAT, WHEN EXECUTED,       *   FILE 317
//*     WILL DELETE THE BOOKSHELVES AND BOOKS INDICATED.            *   FILE 317
//*                                                                 *   FILE 317
//*     PARAMETERS -                                                *   FILE 317
//*                                                                 *   FILE 317
//*     THIS EXEC IS CALLED WITH THE FOLLOWING PARAMETER FORMAT:    *   FILE 317
//*                                                                 *   FILE 317
//*        BKMGRMNT  CNTLFILE                                       *   FILE 317
//*                                                                 *   FILE 317
//*     WHERE:                                                      *   FILE 317
//*                                                                 *   FILE 317
//*        CNTLFILE - IS THE DATA SET NAME OF THE CONTROL FILE.     *   FILE 317
//*                                                                 *   FILE 317
//*     CONTROL FILE FORMATS -                                      *   FILE 317
//*                                                                 *   FILE 317
//*     THE CONTROL FILE CONSISTS OF CONTROL RECORDS THAT           *   FILE 317
//*     DEFINE THE DATA TO BE PROCESSED.                            *   FILE 317
//*                                                                 *   FILE 317
//*     THE BOOKSHELF LISTS, BOOKSHELVES, BOOKSHELF INDEXES, AND    *   FILE 317
//*     BOOKS ARE ACCESSED FROM THE CATALOG.  THE CATALOGED DATA    *   FILE 317
//*     SET NAMES ARE DEFINED BY THE FOLLOWING CONTROL              *   FILE 317
//*     STATEMENTS:                                                 *   FILE 317
//*                                                                 *   FILE 317
//*        CATALOG BKLSHELF=MASK                                    *   FILE 317
//*        CATALOG BKSHELF=MASK                                     *   FILE 317
//*        CATALOG BKINDEX=MASK                                     *   FILE 317
//*        CATALOG BOOK=MASK                                        *   FILE 317
//*                                                                 *   FILE 317
//*     WHERE:                                                      *   FILE 317
//*                                                                 *   FILE 317
//*        MASK - IS A MASK DEFINING THE DATA SET NAMES.  A MASK    *   FILE 317
//*               CONSISTS OF A HIGH-LEVEL QUALIFIER, FOLLOWED BY   *   FILE 317
//*               ONE OR MORE QUALIFIERS.  A PERCENT SIGN ('%')     *   FILE 317
//*               CAN BE USED TO MATCH ANY SINGLE CHARACTER.  AN    *   FILE 317
//*               ASTERISK ('*') CAN BE USED TO MATCH MULTIPLE      *   FILE 317
//*               CHARACTERS IN A SINGLE QUALIFIER.  A DOUBLE       *   FILE 317
//*               ASTERISK ('**') CAN BE USED TO MATCH ZERO OR      *   FILE 317
//*               MORE QUALIFIERS.                                  *   FILE 317
//*                                                                 *   FILE 317
//*     TO EXCLUDE SPECIFIC DATA SETS, USE THE FOLLOWING            *   FILE 317
//*     CONTROL STATEMENTS:                                         *   FILE 317
//*                                                                 *   FILE 317
//*        EXCLUDE BKLSHELF=DSN                                     *   FILE 317
//*        EXCLUDE BKSHELF=DSN                                      *   FILE 317
//*        EXCLUDE BKINDEX=DSN                                      *   FILE 317
//*        EXCLUDE BOOK=DSN                                         *   FILE 317
//*                                                                 *   FILE 317
//*     WHERE:                                                      *   FILE 317
//*                                                                 *   FILE 317
//*        DSN - IS THE DATA SET NAME TO BE EXCLUDED.               *   FILE 317
//*                                                                 *   FILE 317
//*     'SPECIAL' BOOKSHELVES ARE BOOKSHELVES THAT ARE CREATED      *   FILE 317
//*     LOCALLY, USING BOOKS FROM OTHER BOOKSHELVES.  FOR           *   FILE 317
//*     EXAMPLE, THE 'MESSAGES' BOOKSHELF CONTAINS ALL OF THE       *   FILE 317
//*     BOOKS CONTAINING MESSAGES AND CODES FROM THE OTHER          *   FILE 317
//*     BOOKSHELVES.  THE SPECIAL BOOKSHELVES ARE DEFINED BY THE    *   FILE 317
//*     FOLLOWING CONTROL STATEMENT:                                *   FILE 317
//*                                                                 *   FILE 317
//*        SPECIAL BKSHELF=3DDSN                                    *   FILE 317
//*                                                                 *   FILE 317
//*     WHERE:                                                      *   FILE 317
//*                                                                 *   FILE 317
//*        DSN - IS THE DATA SET NAME OF THE SPECIAL BOOKSHELF.     *   FILE 317
//*                                                                 *   FILE 317
//*     THIS EXEC WILL WRITE SEVERAL REPORTS.  THE REPORT DATA      *   FILE 317
//*     SET IS DEFINED FROM THE FOLLOWING CONTROL STATEMENTS:       *   FILE 317
//*                                                                 *   FILE 317
//*        REPORT DSN=DSN                                           *   FILE 317
//*        REPORT SYSOUT=CLASS                                      *   FILE 317
//*        REPORT TERM                                              *   FILE 317
//*        REPORT ALLOC=PARMS                                       *   FILE 317
//*        REPORT OUTDES=OUTDES                                     *   FILE 317
//*        REPORT DEPTH=DEPTH                                       *   FILE 317
//*                                                                 *   FILE 317
//*     WHERE:                                                      *   FILE 317
//*                                                                 *   FILE 317
//*        DSN    - IS THE NAME OF THE DATA SET TO WHICH THE        *   FILE 317
//*                 REPORT IS TO BE WRITTEN.                        *   FILE 317
//*                                                                 *   FILE 317
//*        CLASS  - IS THE SYSOUT CLASS TO WHICH THE REPORT IS      *   FILE 317
//*                 TO BE WRITTEN.                                  *   FILE 317
//*                                                                 *   FILE 317
//*        TERM   - SPECIFIES THAT THE REPORT IS TO BE WRITTEN      *   FILE 317
//*                 TO THE TSO TERMINAL.                            *   FILE 317
//*                                                                 *   FILE 317
//*        PARMS  - SPECIFIES THE PARAMETERS TO BE INCLUDED ON      *   FILE 317
//*                 THE TSO ALLOCATE FOR THE REPORT DATA SET.       *   FILE 317
//*                                                                 *   FILE 317
//*        OUTDES - SPECIFIES THE PARAMETERS TO BE USED FOR THE     *   FILE 317
//*                 TSO OUTDES COMMAND FOR THE REPORT DATA SET.     *   FILE 317
//*                 AN 'OUTDES(BKMGROUT)' PARAMETER WILL BE         *   FILE 317
//*                 INCLUDED IN THE TSO ALLOCATE COMMAND FOR THE    *   FILE 317
//*                 REPORT DATA SET.  THIS PARAMETER CAN BE         *   FILE 317
//*                 SPECIFIED ONLY IF THE 'REPORT SYSOUT'           *   FILE 317
//*                 PARAMETER IS SPECIFIED.                         *   FILE 317
//*                                                                 *   FILE 317
//*        DEPTH  - IS THE NUMBER OF LINES TO BE PRINTED ON A       *   FILE 317
//*                 PAGE.  IF THIS PARAMETER IS OMITTED, THE        *   FILE 317
//*                 DEFAULT IS 60.                                  *   FILE 317
//*                                                                 *   FILE 317
//*     THIS EXEC WILL WRITE ANOTHER REXX EXEC THAT TO DELETE       *   FILE 317
//*     THE UNREFERENCED BOOKSHELVES, BOOKSHELF INDEXES, AND        *   FILE 317
//*     BOOKS.  THE REXX EXEC IS WRITTEN TO A DATA SET DEFINED      *   FILE 317
//*     BY THE FOLLOWING CONTROL STATEMENTS:                        *   FILE 317
//*                                                                 *   FILE 317
//*        EXEC DSN=DSN                                             *   FILE 317
//*        EXEC ALLOC=PARMS                                         *   FILE 317
//*                                                                 *   FILE 317
//*     WHERE:                                                      *   FILE 317
//*                                                                 *   FILE 317
//*        DSN   - IS THE DATA SET NAME OF THE REXX EXEC.  IF       *   FILE 317
//*                THIS PARAMETER IS NOT SPECIFIED, THE EXEC        *   FILE 317
//*                WILL NOT BE WRITTEN.                             *   FILE 317
//*                                                                 *   FILE 317
//*        PARMS - SPECIFIES THE ALLOCATION PARAMETERS TO BE        *   FILE 317
//*                INCLUDED ON THE TSO ALLOCATE FOR THE REXX        *   FILE 317
//*                EXEC DATA SET.  IF THIS PARAMETER IS NOT         *   FILE 317
//*                SPECIFIED, THE DATA SET MUST ALREADY EXIST.      *   FILE 317
//*                                                                 *   FILE 317
```
