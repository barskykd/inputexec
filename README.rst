inputexec
=========

This program aims to handle button/command binding for headless hosts.
It captures all events from an input device (keyboard, mouse, etc.) and runs commands appropriately.

inputexec was born from the need to pass key presses from a remote control to a Music Player Daemon.


Example usage:

.. code-block:: sh

    inputexec --action-commands=actions.ini --source-file=/dev/input/keyboard

The ``--action-commands`` file contains action to map to each keypress:

.. code-block:: ini

    [commands]
    keypress.KEY_PLAYPAUSE = mpc toggle
    keypress.KEY_PREVIOUSSONG = mpc prev
    keypress.KEY_NEXTSONG = mpc next
    keypress.KEY_STOPCD = mpc stop


Installation
------------

inputexec is distributed under the 2-clause BSD license, and needs Python 3.13


From source
"""""""""""

* Clone this repostiory
* Run ``pip install .``


Launching and configuration
---------------------------

inputexec uses only optional arguments; the full list is available through ``inputexec --help``.

All options may also been read from a configuration file passed as ``inputexec --config /path/to/example.ini``.
The list of valid options for the configuration files are available through ``inputexec --dump-config``.


Configuring actions
-------------------

Finding the symbol associated with each key press may be complicated; to solve that problem,
inputexec can run in ``print`` mode:

.. code-block:: sh

    inputexec --source-file=/dev/input/event0 --action-mode=print


Now, each keypress will be displayed on stdout:

.. code-block:: ini

    keypress.KEY_PLAYPAUSE
    keypress.KEY_PREVIOUSSONG
    keypress.KEY_NEXTSONG
    keypress.KEY_STOPCD


Executing actions
-----------------

Three action modes are available, configured through ``--action-mode``:

* ``print``: described above, simply print event lines to stdout
* ``run_sync``: whenever an event occurs, the related command is called;
  this blocks the program
* ``run_async``: One or more threads are started (the number is defined by
  ``--action-jobs``) and commands to run are dispatched between those threads.


Input
-----

inputexec can read from stdin, from a file or from a character device.

For stdin, simply pass ``--source-file=-``

If another file path is provided, inputexec will look at its type and,
if the file is a device node with major 13 (i.e an input device on linux),
use the ``evdev`` reader.
A linux input device can be opened either in ``shared`` mode
(events are propagated to all other readers) or in ``exclusive`` mode;
this behaviour is controlled by the ``--source-mode=exclusive|shared`` flag.

Otherwise, events will be generated from the lines of the file.


Logging and debug
-----------------

inputexec provides a few options for logging, controlled by the ``--logging-target`` flag:

Syslog
  With ``--logging-target=syslog``, all messages are sent to syslog

stderr
  With ``--logging-target=stderr``, data is written to stderr

file
  With ``--logging-target=file --logging-file=FILE``, logs are appended to FILE


Logging verbosity can be adjusted through ``--logging-level=``.
The ``--traceback`` option enables dumping full (Python) stack upon exceptions.




