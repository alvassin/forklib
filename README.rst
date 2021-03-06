forklib
=======

Fork the single process easily

Basic example
+++++++++++++

.. code-block:: python

    import forklib
    import logging
    import os
    from time import sleep


    logging.basicConfig(level=logging.DEBUG)

    def run():
        print(
            "Proceess #{id} has PID: {pid}".format(
                id=forklib.get_id(),
                pid=os.getpid()
            )
        )
        sleep(1)


    def main():
        print("Master proccess has PID: {0}".format(os.getpid()))
        forklib.fork(4, run)



    if __name__ == '__main__':
        main()


This code makes 4 forks. When you try to run it you will see something like this ::

    Master proccess has PID: 39485
    DEBUG:forklib.forking:Starting 4 processes
    Proceess #1 has PID: 39487
    Proceess #0 has PID: 39486
    Proceess #2 has PID: 39488
    Proceess #3 has PID: 39489
    DEBUG:forklib.forking:Child with PID: 39487 Number: 1 exited normally
    DEBUG:forklib.forking:Child with PID: 39489 Number: 3 exited normally
    DEBUG:forklib.forking:Child with PID: 39488 Number: 2 exited normally
    DEBUG:forklib.forking:Child with PID: 39486 Number: 0 exited normally


Forkme will be control forks. When subprocess will be killed or will exit with
non-zero code it will be restarted immediately. e.g.::

    Master proccess has PID: 7579
    INFO:forklib:Starting 4 processes
    Proceess #0 has PID: 7580
    Proceess #1 has PID: 7581
    Proceess #2 has PID: 7582
    Proceess #3 has PID: 7583
    WARNING:forklib:Child with PID: 7580 Number: 0 killed by signal 9, restarting
    Proceess #0 has PID: 7584



Parallel iteration
++++++++++++++++++

You can load the large array of elements on the memory and process it in
multiple processes. After forking the memory will not be copied, instead
this the copy-on-write mechanism will be used.

.. code-block:: python

    from time import sleep

    from forklib import fork_map, fork
    import logging


    logging.basicConfig(level=logging.INFO)


    def map_func(item):
        return item + 1


    def main():
        for item in fork_map(map_func, range(20000), workers=10):
            print(item)

        fork(2, lambda: sleep(1), auto_restart=True)


    if __name__ == '__main__':
        main()


Versioning
++++++++++

This software follows `Semantic Versioning`_

.. _Semantic Versioning: http://semver.org/
