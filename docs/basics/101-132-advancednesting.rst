.. _nesting2:

More on Dataset nesting
^^^^^^^^^^^^^^^^^^^^^^^

You may have noticed how working in the subdataset felt as if you would be
working in an independent dataset -- there was no information or influence at
all from the top-level ``DataLad-101`` superdataset, and you build up a
completely stand-alone history:

.. runrecord:: _examples/DL-101-132-101
   :language: console
   :workdir: dl-101/DataLad-101/midterm_project

   $ git log --oneline

In principle, this is no news to you. From section :ref:`nesting` and the
YODA principles you already know that nesting allows for a modular re-use of
any other DataLad dataset, and that this re-use is possible and simple
precisely because all of the information is kept within a (sub)dataset.

What is new now, however, is that you applied changes to the dataset. While
you already explored the looks and feels of the ``longnow`` subdataset in
previous sections, you now *modified* the contents of the ``midterm_project``
subdataset.
How does this influence the superdataset, and how does this look like in the
superdataset's history? You know from section :ref:`nesting` that the
superdataset only stores the *state* of the subdataset. Upon creation of the
dataset, the very first, initial state of the subdataset was thus recorded in
the superdataset. But now, after you finished your project, your subdataset
evolved. Let's query the superdataset what it thinks about this.

.. runrecord:: _examples/DL-101-132-102
   :language: console
   :workdir: dl-101/DataLad-101/midterm_project

   # move into the superdataset
   $ cd ../
   $ datalad status

From the superdataset's perspective, the subdataset appears as being
"modified". Note that these modifications of the subdataset are not
automatically recorded to the superdataset! This makes sense -- after all it
should be up to you to decide whether you want record something or not --,
but it is worth repeating: If you modify a subdataset, you will need to save
this *in the superdataset* in order to have a clean superdataset status.

This point in time in DataLad-101 is a convenient moment to dive a bit deeper
into the functions of the :command:`datalad status` command. If you are
interested in this, checkout the hidden section below.

.. findoutmore:: More on datalad status

   First of all, let's start with a quick overview of the different content *types*
   and content *states* various :command:`datalad status` commands in the course
   of DataLad-101 have shown up to this point:

   You have seen the following *content types*:

   - ``file``, e.g., ``notes.txt``: any file (or symlink that is a placeholder to an annexed file)
   - ``directory``, e.g., ``books``: any directory that does not qualify for the ``dataset`` type
   - ``symlink``, e.g., the ``.jgp`` that was manually unlocked in section :ref:`run3`:
     any symlink that is not used as a placeholder for an annexed file
   - ``dataset``, e.g., the ``midterm_project``: any top-level dataset, or any subdataset
     that is properly registered in the superdataset

   And you have seen the following *content states*: ``modified`` and ``untracked``.
   The section :ref:`filesystem` will show you many instances of ``deleted`` content
   state as well.

   But beyond understanding the report of :command:`datalad status`, there is also
   additional functionality:
   :command:`datalad status` can handle status reports for a whole hierarchy
   of datasets, and it can report on a subset of the content across any number of
   datasets in this hierarchy by providing selected paths. This is useful as soon
   as datasets become more complex and contain subdatasets with changing contents.

   When performed without any arguments, :command:`datalad status` will report
   the state of the current dataset. However, you can specify a path to any
   sub- or superdataset with the ``--dataset`` option.

   In order to demonstrate this a bit better, we will make sure that not only the
   state of the subdataset *within* the superdataset is modified, but also that the
   subdataset contains a modification. For this, let's add an empty text file into
   the ``midterm_project`` subdataset:

   .. runrecord:: _examples/DL-101-132-103
      :language: console
      :workdir: dl-101/DataLad-101

      $ touch midterm_project/an_empty_file

   If you are in the root of ``DataLad-101``, but interested in the status
   *within* the subdataset, simply provide a path (relative to your current location)
   to the command:

   .. runrecord:: _examples/DL-101-132-104
      :language: console
      :workdir: dl-101/DataLad-101

      $ datalad status midterm_project

   Alternatively, to achieve the same, specify the superdataset as the ``--dataset``
   and provide a path to the subdataset *with a trailing path separator* like
   this:

   .. runrecord:: _examples/DL-101-132-105
      :language: console
      :workdir: dl-101/DataLad-101

      $ datalad status -d . midterm_project/

   Note that both of these commands return only the ``untracked`` file and not
   not the ``modified`` subdataset because we're explicitly querying only the
   subdataset for its status.
   If you however, as done outside of this hidden section, you want to know about
   the subdataset record in the superdataset without causing a status query for
   the state *within* the subdataset itself, you can also provide an explicit
   path to the dataset (without a trailing path separator). This can be used
   to specify a specific subdataset in the case of a dataset with many subdatasets:

   .. runrecord:: _examples/DL-101-132-106
      :language: console
      :workdir: dl-101/DataLad-101

      $ datalad status -d . midterm_project


   But if you are interested in both the state within the subdataset, and
   the state of the subdataset within the superdataset, you can combine the
   two paths:

   .. runrecord:: _examples/DL-101-132-107
      :language: console
      :workdir: dl-101/DataLad-101

      $ datalad status -d . midterm_project midterm_project/

   Finally, if these subtle differences in the paths are not easy to memorize,
   the ``-r/--recursive`` option will also report you both status aspects:

   .. runrecord:: _examples/DL-101-132-108
      :language: console
      :workdir: dl-101/DataLad-101

      $ datalad status --recursive

   This still was not all of the available functionality of the
   :command:`datalad status` command. You could for example adjust whether and
   how untracked dataset content should be reported with the ``--untracked``
   option, or get additional information from annexed content with the ``--annex``
   option. To get a complete overview on what you could do, check out the technical
   documentation of :command:`datalad status` `here <http://docs.datalad.org/en/latest/generated/man/datalad-status.html>`_.

   Before we leave this hidden section, lets undo the modification of the subdataset
   by removing the untracked file:

   .. runrecord:: _examples/DL-101-132-109
      :language: console
      :workdir: dl-101/DataLad-101

      $ rm midterm_project/an_empty_file
      $ datalad status --recursive

Let's save the modification of the subdataset into the history of the
superdataset:

.. runrecord:: _examples/DL-101-132-110
   :language: console
   :workdir: dl-101/DataLad-101/

   $ datalad save -m "finished my midterm project"

Let's check which subproject commit is now recorded in the superdataset:

.. runrecord:: _examples/DL-101-132-112
   :language: console
   :workdir: dl-101/DataLad-101/

   $ git log -p -n 1

As you can see in the log entry, the subproject commit changed from the
first commit hash in the subdataset history to the most recent one. With this
change, therefore, your superdataset tracks the most recent version of
the ``midterm_project`` dataset, and your dataset's status is clean again.
