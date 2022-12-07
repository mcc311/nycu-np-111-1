Project 1: NPShell
==================

Introduction
------------
In this project, you are asked to design a shell named **npshell**. The npshell should support the
following features:

1. Execution of commands
2. Ordinary Pipe
3. Numbered Pipe
4. File Redirection

.. note::
  More details will be defined in :ref:`Section 3 <Specification>`.



Scenario of using npshell
-------------------------

Structure of Working Directory
##############################

Example:

.. code-block:: none
    
    working_dir
    |-- bin # Executables may be added to or removed from bin/
    | |-- cat
    | |-- ls
    | |-- noop # A program that does nothing
    | |-- number # Add a number to each line of input
    | |-- removetag # Remove HTML tags and output to STDOUT
    | |-- removetag0 # Same as removetag, but outputs error messages to STDERR
    |-- test.html
    |-- npshell


Scenario
########

.. collapse:: The following is a scenario of using the npshell.

    .. code-block:: none

        $ ./npshell # execute your npshell
        % printenv PATH # initial PATH is bin/ and ./
        bin:.
        % setenv PATH bin # set PATH to bin/ only
        % printenv PATH
        bin
        % ls
        bin npshell test.html
        % ls bin
        cat ls noop number removetag removetag0
        % cat test.html > test1.txt
        % cat test1.txt
        <!test.html>
        <TITLE>Test</TITLE>
        <BODY>This is a <b>test</b> program
        for ras.
        </BODY>
        % removetag test.html
        Test
        This is a test program
        for ras.
        % removetag test.html > test2.txt
        % cat test2.txt
        Test
        This is a test program
        for ras.
        % removetag0 test.html
        Error: illegal tag "!test.html"
        Test
        This is a test program
        for ras.
        % removetag0 test.html > test2.txt
        Error: illegal tag "!test.html"
        % cat test2.txt
        Test
        This is a test program
        for ras.
        % removetag test.html | number
        1
        2 Test
        3 This is a test program
        4 for ras.
        5
        % removetag test.html |1 # pipe STDOUT to the first command of next line
        % number # STDIN is from the previous pipe (removetag)
        1
        2 Test
        3 This is a test program
        4 for ras.
        5
        % removetag test.html |2 # pipe STDOUT to the first command of next next line
        % ls
        bin npshell test1.txt test2.txt test.html
        % number # STDIN is from the previous pipe (removetag)
        1
        2 Test
        3 This is a test program
        4 for ras.
        5
        % removetag test.html |2 # pipe STDOUT to the first command of next next line
        % removetag test.html |1 # pipe STDOUT to the first command of next line
        # (merge with the previous one)
        % number # STDIN is from the previous pipe (both two removetag)
        1
        2 Test
        3 This is a test program
        4 for ras.
        5
        6
        7 Test
        8 This is a test program
        9 for ras.
        10
        % removetag test.html |2
        % removetag test.html |1
        % number |1
        % number
        1 1
        2 2 Test
        3 3 This is a test program
        4 4 for ras.
        5 5
        6 6
        7 7 Test
        8 8 This is a test program
        9 9 for ras.
        10 10
        % removetag test.html | number |1
        % number
        1 1
        2 2 Test
        3 3 This is a test program
        4 4 for ras.
        5 5
        % removetag test.html |2 removetag test.html |1 # number pipe may occur in middle
        % number |1 number
        1 1
        2 2 Test
        3 3 This is a test program
        4 4 for ras.
        5 5
        6 6
        7 7 Test
        8 8 This is a test program
        9 9 for ras.
        10 10
        % ls |2
        % ls
        bin npshell test1.txt test2.txt test.html
        % number > test3.txt
        % cat test3.txt
        1 bin
        2 npshell
        3 test1.txt
        4 test2.txt
        5 test.html
        % removetag0 test.html |1
        Error: illegal tag "!test.html" # output error message to STDERR
        % number
        1
        2 Test
        3 This is a test program
        4 for ras.
        5
        % removetag0 test.html !1 # pipe both STDOUT and STDERR
        # to the first command of the next line
        % number
        1 Error: illegal tag "!test.html"
        2
        3 Test
        4 This is a test program
        5 for ras.
        6
        % date
        Unknown command: [date].
        # TA manually moves the executable "date" into $working_dir/bin/
        % date
        Mon Oct 4 15:12:35 CST 2022
        % exit

Specification
-------------

NPShell Behavior
################

1. Use **”% ”** as the command line prompt. Notice that there is one space character after **%**.
2. The npshell parses the inputs and executes commands.
3. The npshell terminates after receiving the **exit** command or **EOF**.
4. There will **NOT** exist the test case that commands need to read from **STDIN**.