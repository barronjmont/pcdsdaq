Using the DAQ with Bluesky
==========================
The `Daq` class is designed as a drop-in ``bluesky`` readable. That means
you can put it into built-in ``bluesky`` plans in the ``dets`` list and it will
collect data at each scan point.

This document will assume some familiarity with ``bluesky`` and
how to use the ``RunEngine``, but does not require a full understanding of
the internals.


Creating a Daq object with the RunEngine
----------------------------------------
The `Daq` needs to register a ``RunEngine`` instance for this to work. This
must be the same ``RunEngine`` that will be running all of the plans.

.. code-block:: python

    from bluesky import RunEngine
    from pcdsdaq.daq import Daq
    from pcdsdaq.sim import set_sim_mode

    set_sim_mode(True)
    RE = RunEngine({})
    daq = Daq(RE=RE)


.. ipython:: python
    :suppress:

    from bluesky import RunEngine
    from ophyd.sim import motor1
    from ophyd.sim import det1
    from pcdsdaq.daq import Daq
    from pcdsdaq.sim import set_sim_mode

    set_sim_mode(True)
    RE = RunEngine({})
    daq = Daq(RE=RE)


.. note::

   The ``daq`` object must be staged if it's going to be used in a plan. This
   is done automatically in `daq_during_wrapper`, `daq_during_decorator`, and
   most built-ins like ``count`` and ``scan``.


Calib Cycles
------------
Including calib cycles in a built-in plan is as simple as including the `Daq`
as a reader or detector. The `Daq` will start and run for the configured
duration or number of events at every scan step.

The built-in ``scan`` will move ``motor1`` from ``0`` to ``10`` in ``11``
steps. Prior to the scan, we configure the ``daq`` to take ``120`` events at
each point. Since ``daq`` is included in the detectors list, it is run at every
step.

.. code-block:: python

    from bluesky.plans import scan
    daq.configure(events=120)


.. ipython:: python
    :suppress:

    from bluesky.plans import scan
    daq.configure(events=120)


.. ipython:: python

    RE(scan([daq], motor1, 0, 10, 11))


Running for an Entire Plan Duration
-----------------------------------
The simplest way to include the daq is to turn it on at the start of the plan
and turn it off at the end of the plan. This is done using the
`daq_during_wrapper` or `daq_during_decorator`, which treat
the `Daq` as a ``bluesky`` ``Flyer``.

.. code-block:: python

    from bluesky.plan_stubs import mv
    from bluesky.preprocessors import run_decorator
    from pcdsdaq.preprocessors import daq_during_decorator

    @daq_during_decorator()
    @run_decorator()
    def basic_plan(motor, start, end):
        yield from mv(motor, start)
        yield from mv(motor, end)
        yield from mv(motor, start)


.. ipython:: python
    :suppress:

    from bluesky.plan_stubs import mv
    from bluesky.preprocessors import run_decorator
    from pcdsdaq.preprocessors import daq_during_decorator

    @daq_during_decorator()
    @run_decorator()
    def basic_plan(motor, start, end):
        yield from mv(motor, start)
        yield from mv(motor, end)
        yield from mv(motor, start)


This plan will start the `Daq`, move ``motor`` to the ``start``, ``end``,
and back to the ``start`` positions, and then end the run.

.. ipython:: python

    RE(basic_plan(motor1, 0, 10))


If you ignore the `daq_during_decorator`, this is just a normal ``plan``.
This makes it simple to add the daq collecting data in the background
to a normal ``bluesky`` ``plan``.
