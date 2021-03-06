|morpheus| UI not loading after upgrade or reconfigure
======================================================

Problem:
  The |morpheus| ui does not load after performing an upgrade.

Common Causes:
   #. The morpheus-ui has not finished loading
   #. The morpheus-ui was not fully stopped before reconfigure, or not started after reconfigure
   #. |morpheus| was forced to restart or shut down while the database schema was being migrated during an upgrade

Solutions:
  #. The morpheus-ui has not finished loading.

      An easy way to see when the ui is finished loading and running is to tail the ui current file and look for the morpheus logo with version and start time

      .. code-block:: bash

        morpheus-ctl tail morpheus-ui

  .. NOTE:: After running `morpheus-ctl start morpheus-ui`, the |morpheus| ui takes around 3 minutes to run depending on hardware.

  #. The morpheus-ui was not fully stopped before reconfigure, or not started after reconfigure

      The morpheus ui must be stopped prior to running morpheus-ctl reconfigure when upgrading. Sometimes running morpheus-ctl stop morpheus-ui will timeout and the ui is not actually stopped. If stopping the ui does timeout, run morpheus-ctl kill morpheus-ui prior to reconfigure, and be sure to run morpheus-ctl start morpheus-ui after reconfigure is completed.


      If you ran a reconfigure before stopping the ui, run:

      .. code-block:: bash

        sudo morpheus-ctl kill morpheus-ui
        sudo morpheus-ctl reconfigure
        sudo morpheus-ctl start morpheus-ui

      Wait for the ui to come up.

  #. |morpheus| was forced to restart or shut down while the database schema was being migrated during an upgrade

      If the ui fails to start and you see the error ``Invocation of init method failed; nested exception is liquibase.exception.LockException: Could not acquire change log lock.  Currently locked by morpheus`` it likely means morpheus was forced to restart or shut down while the database schema was being migrated during an upgrade, and the lock was not released.

      To release the lock, you will need to run a mysql query. You will need to install mysql-client on the morpheus appliance, and grab the password for morpheus mysql. The username and db name are both morpheus. The password to login to mysql can be found in the application.yml file located at ``/opt/morpheus/conf/application.yml``

      Then run the following:

      .. code-block:: bash

        mysql -u morpheus -p -h 127.0.0.1 morpheus

      At the prompt, enter the mysql password from the application.yml

      Then run:

      .. code-block:: bash

        DELETE FROM DATABASECHANGELOGLOCK;

      Then restart morpheus-ui:

      .. code-block:: bash

        sudo morpheus-ctl restart morpheus-ui

      If the restart timesout, run:

      .. code-block:: bash

        sudo morpheus-ctl kill morpheus-ui
        sudo morpheus-ctl start morpheus-ui
