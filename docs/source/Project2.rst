Project2: Remote Working Ground (rwg) Server
============================================

Introduction
------------

In this project, you are asked to design 3 kinds of servers:

1. Design a **Concurrent connection-oriented server**. This server allows one client connect to it.
2. Design a server of the chat-like systems, called `remote working systems (rwg)`. In this system, users can communicate with other users. You need to use the **single-process concurrent** paradigm to design this server.
3. Design the rwg server using the **concurrent connection-oriented** paradigm with shared memory and **FIFO**.

.. note::

    These three servers must support all functions in project 1.

Scenario of Part One
--------------------
| You can use telnet to connect to your server.
| Assume your server is running on nplinux1 and listening at port 7001.

.. collapse:: Example:
    :open:

    .. code-block:: bash

        $ telnet nplinux1.cs.nctu.edu.tw 7001
        % ls | cat
        bin test.html
        % ls |1
        % cat
        bin test.html
        % exit
        $

Scenario of Part Two and Three
------------------------------

Introduction of Requirements
############################

You are asked to design the following features in your server.

1. Pipe between different users. Broadcast message whenever a user pipe is used. 
2. Broadcast message of login/logout information.
3. New built-in commands:
    - ``who``: show information of all users. 
    - ``tell``: send a message to another user. 
    - ``yell``: send a message to all users.
    - ``name``: change your name.
4. All commands in project 1

.. note::
    More details will be defined in :ref:`Specification<Specification>`.

Scenario
########

| The following is  scenarios for different user- `User1` & `User2`, of using the rwg system.
| Assume your server is running on `nplinux1` and listening at port `7001`.


.. collapse:: User1:
    
    .. code-block:: bash

        $ telnet nplinux1.nctu.edu.tw 7001
        ****************************************
        ** Welcome to the information server. **
        ****************************************
        *** User ’(no name)’ entered from 140.113.215.62:1201. ***  # Broadcast message of user login
        % who
        <ID>    <nickname>  <IP:port>           <indicate me>
        1       (no name)   140.113.215.62:1201 <-me
        % name Pikachu
        *** User from 140.113.215.62:1201 is named ’Pikachu’. ***
        % ls
        bin test.html
        % *** User ’(no name)’ entered from 140.113.215.63:1013. *** # User 2 logins
        who
        <ID>    <nickname>  <IP:port>           <indicate me>
        1       Pikachu       140.113.215.62:1201 <-me
        2       (no name)   140.113.215.63:1013
        % *** User from 140.113.215.63:1013 is named ’Snorlax’. ***
        who
        <ID>    <nickname>  <IP:port>           <indicate me>
        1       Pikachu       140.113.215.62:1201 <-me
        2       Snorlax       140.113.215.63:1013
        % *** User ’(no name)’ entered from 140.113.215.64:1302. *** # User 3 logins
        who
        <ID>    <nickname>  <IP:port>           <indicate me>
        1 Pikachu
        2 Snorlax
        3 (no name)
        % yell Who knows how to do project 2? help me plz!
        *** Pikachu yelled ***: Who knows how to do project 2? help me plz!
        % *** (no name) yelled ***: Sorry, I don’t know. :-(         # User 3 yells
        *** Snorlax yelled ***: I know! It’s too easy!                 # User 2 yells
        % tell 2 Plz help me, my friends!
        % *** Snorlax told you ***: Yeah! Let me show you how to send files to you!
        *** Snorlax (#2) just piped ’cat test.html >1’ to Pikachu (#1) *** # Broadcast message of user pipe
        *** Snorlax told you ***: You can use ’cat <2’ to show it!
        cat <5                                                       # mistyping
        *** Error: user #5 does not exist yet. ***
        % cat <2                                                     # receive from the user pipe
        *** Pikachu (#1) just received from Snorlax (#2) by ’cat <2’ ***
        <!test.html>
        <TITLE>Test<TITLE>
        <BODY>This is a <b>test</b> program for rwg.</BODY>
        % tell 2 It’s works! Great!
        % *** Snorlax (#2) just piped ’number test.html >1’ to Pikachu (#1) ***
        *** Snorlax told you ***: You can receive by your program! Try ’number <2’!
        number <2
        *** Pikachu (#1) just received from Snorlax (#2) by ’number <2’ ***
        1 1 <!test.html>
        2 2 <TITLE>Test<TITLE>

.. collapse:: User2:

    .. code-block:: bash

        $ telnet nplinux1.nctu.edu.tw 7001 # The server port number
        ****************************************
        ** Welcome to the information server. **
        ****************************************
        *** User ’(no name)’ entered from 140.113.215.63:1013. ***
        % name Snorlax
        *** User from 140.113.215.63:1013 is named ’Snorlax’. ***
        % *** User ’(no name)’ entered from 140.113.215.64:1302. ***
        who
        <ID>    <nickname>  <IP:port>           <indicate me>
        1 Pikachu
        2 Snorlax
        3 (no name)
        % *** Pikachu yelled ***: Who knows how to do project 2? help me plz!
        *** (no name) yelled ***: Sorry, I don’t know. :-(
        yell I know! It’s too easy!
        *** Snorlax yelled ***: I know! It’s too easy!
        % *** Pikachu told you ***: Plz help me, my friends!
        tell 1 Yeah! Let me show you how to send files to you!
        % cat test.html >1 # write to the user pipe
        *** Snorlax (#2) just piped ’cat test.html >1’ to Pikachu (#1) ***
        % tell 1 You can use ’cat <2’ to show it!
        % *** Pikachu (#1) just received from Snorlax (#2) by ’cat <2’ ***
        *** Pikachu told you ***: It’s works! Great!
        number test.html >1
        *** Snorlax (#2) just piped ’number test.html >1’ to Pikachu (#1) ***
        % tell 1 You can receive by your program! Try ’number <2’!
        % *** Pikachu (#1) just received from Snorlax (#2) by ’number <2’ ***
        *** Pikachu told you ***: Cool! You’re genius! Thank you!
        tell 1 You’re welcome!
        % exit
        $        


Specification
-------------

Working Directory
#################

.. code-block:: bash

    your_working_directory
    |---- bin
    | |-- cat
    | |-- ls
    | |-- noop
    | |-- number
    | |-- removetag
    |
    |---- user_pipe  (Created by the test script)
    | |-- (your user pipe files)
    |
    |-- test.html

Format of the Commands
######################

- ``who``: Show information of all users.
    Output Format:

    .. code-block:: bash

        <ID>[Tab]<nickname>[Tab]<IP:port>[Tab]<indicate me> #Sort by ID in ascending order.
        (1st id)[Tab](1st  name)[Tab](1st IP:port)([Tab](<-me))
        (2nd id)[Tab](2nd  name)[Tab](2nd IP:port)([Tab](<-me))
        (3rd id)[Tab](3rd  name)[Tab](3rd IP:port)([Tab](<-me))
        ...

    Example:

    .. code-block:: bash
        
        % who
        <ID>    <nickname>  <IP:port>   <indicate me>
        1 IamStudent
        2 (no name)
        3 student3
        140.113.215.62:1201 <-me
        140.113.215.63:1013
        140.113.215.62:1201

    .. note::
        | the delimiter of each column is a **Tab**, and the first column represents the login user-id. The user’s id should be assigned in the range of 1-30.
        
    Your server should always assign the smallest unused id to a new user.
    
    Example:

    .. code-block:: bash

        <new user login> # server assigns this user id = 1
        <new user login> # server assigns this user id = 2
        <user 1 logout>
        <new user login> # server assigns this user id = 1, not 3

- **tell <user id> <message>**:
    The user will get the message with following format:

    .. code::
        
        *** <sender’s name> told you ***: <message>