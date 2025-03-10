The semantic versioning
=======================

During the course of development, the implementation of (new) features in NEST Desktop will be reviewed for
compatibility. In this concept a general rule of the semantic versioning NEST Desktop was introduced that the
operational capability of the application can be guaranteed.

.. warning::
   Please be aware of the differences to the official `Semantic versioning <https://semver.org/>`__ standard!

The formal convention of the version releases for specifying compatibility in NEST Desktop uses a three-part number:

A major number
--------------

It uses a new framework, it might be not backward-compatible to older versions. In this case, stored data could be lost.
However, it is able to import older data.

A minor number
--------------

It is a breaking feature such as a new library or a minor changes of the data structure. It means that this version
could cause the compatibility issues and the user might reset the data of the page.

A patch number
--------------

It is a bugfix and non-breaking features were added to the code. The user is able to work with different patch versions
of NEST Desktop and NEST Simulator.
