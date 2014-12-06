Rollback and teardown in bash
=============================

Simple rollback
---------------

    #!/bin/bash
    
    . /path/to/rollback
    
    # create relevant functions:
    #   - rollback to initiate rollback
    #   - rb to add rollback commands
    createRollback rollback rb
    
    # do something
    
    trap rollback INT TERM EXIT
    
    	# create a random file
    	touch example
    
    	# remove the file if something fails
    	rb rm example
    
    	# create a random file
    	touch example2
    
    	# remove the file if something fails
    	rb rm example2
    
    	# try removing a file, but do not fail if it does not exist
    	rm nonexistent || true
    
    	# this will fail
    	rm nonexistent
    
    	# nothing here gets executed
    	# the files example and example2 will get removed
    
    trap - INT TERM EXIT
    
    # do something if everything is successful, in this example never gets executed


Teardown
--------

Teardown works just like rollback:

    #!/bin/bash
    
    . /path/to/rollback
    
    # create relevant functions:
    #   - rollback to initiate rollback
    #   - rb to add rollback commands
    #   - teardown to initiate teardown
    #   - td to add teardown commands
    #   - rt to add a command to both the rollback and teardown stack
    #   - saveTeardown to save the teardown stack into a file
    createRollback rollback rb teardown td rt saveTeardown
    
    # do something
    
    removeFile()
    {
    	rm example
    }
    
    trap rollback INT TERM EXIT
    
    	touch example
    
    	# you can also use functions
    	rt removeFile
    
    	# quote anything that should not be evaluated
    	td 'rm nonexistent || true'
    
    	# ...
    
    trap - INT TERM EXIT
    
    # do something
    
    # run the teardown commands to remove the files
    teardown
    
    # alternatively you could save the commands into a file for later execution:
    # saveTeardown script.teardown


Failures
--------

In case of a failure in one of the rollback or teardown commands execution will stop and all commands from all created rollback and teardown stacks will be printed to stderr.

The saved teardown script will just stop.
