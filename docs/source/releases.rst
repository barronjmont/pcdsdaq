Release History
###############

Next Release
============

Features
--------
- Allow ``ctrl+c`` during a `begin` call with ``wait=True`` to stop the run.
- Add sourcable `pcdsdaq_lib_setup` script that will get `pydaq` and `pycdb`
  ready for your python environment.
- The `connect` method will provide more helpful error messages when it fails.
- Allow the `Daq` class to be used as a ``bluesky`` readable device.
  Once staged, runs will end on run stop documents.
  A calibcycle will be run when the `Daq` is triggered, and triggering will be
  reported as done when the `Daq` has stopped. This means it is viable to use
  the `Daq` inside normal plans like ``scan`` and ``count``.

API Changes
-----------
- ``calib_cycle`` and related ``plans`` module has been removed, as using the
  `Daq` as a readable device is more intuitive and it's still early enough to
  break my API.
- ``daq_wrapper`` and ``daq_decorator`` have been move to the ``preprocessors``
  submodule, as a parallel to the ``bluesky`` structure. They have been renamed
  to `daq_during_wrapper` and `daq_during_decorator` as a parallel to the
  built-in ``fly_during_wrapper``. These are now simple preprocessors to
  run the daq at the same time as a daq-agnostic plan.
- ``complete`` no longer ends the run. This makes it more in line with the
  ``bluesky`` API.

Deprecations
------------
- The daq no longer needs to be passed a ``platform`` argument. This argument
  will be removed in a future release, and will log a warning if you pass it.

v1.2.0 (2018-05-08)
===================

Features
--------
- Add the ``record`` option to the `begin` method. This allows a user running
  interactively to concisely activate recording for single runs.

v1.1.0 (2018-03-07)
===================

Features
--------
- Add ``daq.record`` property to schedule that the next run sould be
  configured with ``record=True``

Bugfixes
--------
- Fix bug where configured record was overridden on every configure

v1.0.0 (2018-03-02)
===================

- Initial release, transferred from `<https://github.com/pcdshub/pcdsdevices>`_
