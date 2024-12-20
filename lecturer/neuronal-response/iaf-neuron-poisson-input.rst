Poisson input into single neurons
=================================

In models, we often assume that pre-synaptic action potentials are arriving randomly at a certain rate. Synapses on the
dendrite of the postsynaptic neuron are then activated in a random fashion. The stochastic process that reflects such
random arrival of point-like events is called a Poisson process. Its only parameter is the rate at which events occur.
In view of the linearity of temporal and spatial dendritic integration, all synapses that have the same amplitude and
sign can be treated as a single compound source of input.

You can now employ the Poisson generator functionality of NEST to explore the response behavior of neurons:

----

.. question::

   1. Devise a method to display simulated Poisson spike trains, which will later be used as a source of input to our model
      neuron. Verify the randomness of the Poisson generator by repeating the same simulation multiple times.

      A raster display with lines corresponding to “trials” rather than “neurons” represents an adequate tool to illustrate
      this.

.. example::
   :collapsible:

   .. md-tab-set::

      .. md-tab-item:: Lab book

         |

         .. image:: /_static/img/screenshots/lecture/single-neuron-poisson-input-1-1.png

      .. md-tab-item:: Code

         .. code-block:: Python

            nest.ResetKernel()

            # Set simulation kernel
            nest.SetKernelStatus({
               "resolution": 0.1,
            })

            # Create nodes
            n1 = nest.Create("iaf_psc_alpha", 100)
            pg1 = nest.Create("poisson_generator", params={
               "rate": 67000,
            })
            vm1 = nest.Create("voltmeter")
            sr1 = nest.Create("spike_recorder")

            # Connect nodes
            nest.Connect(pg1, n1)
            nest.Connect(n1, sr1)
            nest.Connect(vm1, n1, conn_spec={
               "rule": "fixed_outdegree",
               "outdegree": 1,
            })

            # Run simulation
            nest.Simulate(1000)

      .. md-tab-item:: Activity

         .. image:: /_static/img/screenshots/lecture/single-neuron-poisson-input-1-2.png


|

.. question::

   2. Consider an LIF neuron that receives Poisson input of a constant rate using a synapse of a specific amplitude.
      Analyze how the input rate influences the membrane potential and the spiking response of the neuron. The parameters
      of interest are the mean and the variance of the membrane potential, as well as the output firing rate and the
      irregularity of the output spike train.

      What happens if you change the strength of the synapse?

.. example::
   :collapsible:

   .. md-tab-set::

      .. md-tab-item:: Lab book

         |

         .. image:: /_static/img/screenshots/lecture/single-neuron-poisson-input-2-1.png

      .. md-tab-item:: Code

         .. code-block:: Python

            nest.ResetKernel()

            # Set simulation kernel
            nest.SetKernelStatus({
               "resolution": 0.1,
            })

            # Create nodes
            n1 = nest.Create("iaf_psc_alpha", 100)
            n2 = nest.Create("iaf_psc_alpha", 100)
            pg1 = nest.Create("poisson_generator", params={
               "rate": 70000,
            })
            pg2 = nest.Create("poisson_generator", params={
               "rate": 700,
            })
            sr1 = nest.Create("spike_recorder")
            sr2 = nest.Create("spike_recorder")
            vm1 = nest.Create("voltmeter")
            vm2 = nest.Create("voltmeter")

            # Connect nodes
            nest.Connect(pg1, n1)
            nest.Connect(pg2, n2, syn_spec={
               "weight": 100,
            })
            nest.Connect(n1, sr1)
            nest.Connect(n2, sr2)
            nest.Connect(vm1, n1, conn_spec={
               "rule": "fixed_outdegree",
               "outdegree": 1,
            })
            nest.Connect(vm2, n2, conn_spec={
               "rule": "fixed_outdegree",
               "outdegree": 1,
            })

            # Run simulation
            nest.Simulate(1000)

      .. md-tab-item:: Activity

         .. image:: /_static/img/screenshots/lecture/single-neuron-poisson-input-2-2.png

|

.. question::

   3. Now we consider the more realistic situation that a neuron receives input from two different and independent
      presynaptic populations, one consisting of excitatory, the other one consisting of inhibitory neurons.

      .. note::
         The presynaptic population of a cortical nerve cell can be quite large, comprising up to 10,000 neurons,
         say.

      What matters for the postsynaptic neuron is the accumulated spike rate for each type of input, so these input rates
      will also be large. The model has two parameters, the rate :math:`\lambda_{E}` of the excitatory Poisson process and
      the rate :math:`\lambda_{I}` of the inhibitory Poisson process.

      Begin your simulation experiments by fixing :math:`\lambda_{E} = \lambda_{I}` assuming exactly the same firing rate
      for excitatory and inhibitory inputs. Start with small rates (subthreshold) and jointly increase them step by step
      until output spikes are generated (superthreshold). Describe your observations for weak and for strong input, both on
      the level of the membrane potential and on the level of output spike trains.

.. example::
   :collapsible:

   .. md-tab-set::

      .. md-tab-item:: Lab book

         |

         .. image:: /_static/img/screenshots/lecture/single-neuron-poisson-input-3-1.png

      .. md-tab-item:: Code

         .. code-block:: Python

            nest.ResetKernel()

            # Set simulation kernel
            nest.SetKernelStatus({
               "resolution": 0.1,
            })

            # Create nodes
            n1 = nest.Create("iaf_psc_alpha")
            pg1 = nest.Create("poisson_generator", params={
               "rate": 10,
            })
            pg2 = nest.Create("poisson_generator", params={
               "rate": 10,
            })
            vm1 = nest.Create("voltmeter")

            # Connect nodes
            nest.Connect(pg1, n1, syn_spec={
               "weight": -1,
            })
            nest.Connect(pg2, n1)
            nest.Connect(vm1, n1)

            # Run simulation
            nest.Simulate(1000)

      .. md-tab-item:: Activity

         .. md-tab-set::

            .. md-tab-item:: Rate: 10 Hz

               .. image:: /_static/img/screenshots/lecture/single-neuron-poisson-input-3-2.png

            .. md-tab-item:: Rate: 100 Hz

               .. image:: /_static/img/screenshots/lecture/single-neuron-poisson-input-3-3.png

            .. md-tab-item:: Rate: 1000 Hz

               .. image:: /_static/img/screenshots/lecture/single-neuron-poisson-input-3-4.png


|

.. question::

   4. Considering synaptic bombardment from a large pool of presynaptic neurons, the mathematical model of shotnoise is
      appropriate to describe membrane potential fluctuations. Generally, the two relevant parameters :math:`\lambda_{E}`
      and :math:`\lambda_{I}` are fixed independently, and combinations with :math:`\lambda_{E} \neq \lambda_{I}` may
      arise.

      Previously, we have considered Gaussian White Noise input, which was described by the two parameters mean :math:`\mu`
      and variance :math:`\sigma^{2}`. No specific assumptions were then made about the biophysical origin of membrane
      potential fluctuations.

      Shotnoise can also be described in terms of the mean :math:`\mu` and variance :math:`\sigma^{2}` of the membrane
      potential. As long as the input remains subthreshold and no output spikes are generated, it holds that :math:`\mu
      \sim \lambda_{E} - \lambda_{I}` and :math:`\sigma^{2} \sim \lambda_{E} + \lambda_{I}`. (The symbol :math:`\sim` means
      “is proportional to”.)

      Perform some experiments that illustrate this relation.

|

.. question::

   5. If input rates are large enough, output spikes are generated. There is a loose correspondence between the mean
      membrane potential and the mean output rate, as well as between the membrane potential fluctuations (variance) and
      the variability of output spike trains (irregularity).

      One speaks of the “mean-driven regime” and the “fluctuation-driven regime”, depending on whether spikes are
      predominantly generated by a depolarizing drive (mean), or by membrane potential fluctuations (variance),
      respectively.

      Explore the meaning of these two terms, and illustrate the two regimes by suitable simulations. Develop criteria
      that allow you to classify neuronal activity recorded in experiments accordingly.

----

NEST Desktop does not only offer direct current (DC) stimulators, but also noise current stimulators. In principle, they
are used in the same way as a DC stimulator. “Poisson input” is just one specific form of “noise input”. Technically,
this is correct, and this immediately explains how to use it in a simulation: Just replace the direct current stimulator
by a Poisson stimulator.

Biologically, however, we are now talking about an input that works with spikes that activate synapses, and it does not
just inject electrical charge into the cell. Therefore, changing the properties of synapses on the target neuron also
changes the properties of the “noise” input. This may be confusing, but the concept of “shotnoise” exactly reflects
this. You should also keep in mind that the spikes generated by a Poisson source typically originate from a large set of
presynaptic neurons. In the neocortex, this set could comprise hundreds or thousands of presynaptic neurons, and the
rate parameter can assume very large values (rate of individual neurons :math:`×` number of presynaptic neurons).

Noise and fluctuations in our simulations are based on so-called “pseudo-random” numbers. They look like “true” random
numbers for all practical purposes, but they are generated by a perfectly deterministic algorithm, one after the other.
Using the same starting point (:code:`seed`), you get exactly the same stream of random numbers. However, if you want a
different stream of random numbers each time you perform the simulation, select :bdg:`Randomize seed` in the
“Simulation” controller.
