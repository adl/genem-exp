Application 2: Probabilistic Model Checking
-------------------------------------------

This directory contains artifacts for the benchmark of application 2 (probabilistic model checking) in our ATVA'19 submission titled *Generic Emptiness Check for Fun and Profit*.

This benchmark was run with an extension of PRISM version 4.4. A tarball of this version is given below. The benchmarks were run with the explicit engine of PRISM on a computer with two Intel E5-2680 8-core CPUs at 2.70~GHz with 384GB of RAM running Linux and a time-out of $30\,\mathrm{min}$ and 10GB memory limit.

The following files are supplied:
- [`logs.tar.gz`]: The output of PRISM during the benchmark
- [`mutual4.ltl`]: The LTL formulas used in the benchmark in prefix form. Please note, that formulas, we used with $\mathit{Pr}^{\min}$ in the benchmarks, are negated, as this is also done by PRISM
- [`mutual4.nm`]: The model file for the mutual exclusion problem
- [`mutual4.pctl`]: The properties used in the benchmark
- [`pmc.csv`]: Result of the experiment as a CSV file. Each line correspond to a property and a tool used for the automata generation. The following columns are used:
    - `formula`: the property to be checked
    - `tool`: the tool we used to generate the automata: `ltl2tgba` stands for SPOT (version 2.7.4) and the columns `Emerson-Lei` in the paper. `rabinizer` stands for Rabinizer and the columns `Generalized Rabin` in the paper. `std` stands for the PRISM internal ltl2dstar version and the columns `Rabin` in the paper
    - `MC time`: the overall model checking time in s
    - `MEC checking time`: the run-time for the MEC analysis in seconds. In case of `ltl2tgba` our algorithm is used, in the other cases the standard internal PRISM algorithm for generalized Rabin or Rabin acceptance. Please note, that in the lines where `ltl2tgba` has been used as tool, we just write `-`, as the according running time is covered by `MEC checking time EL`
    - `MEC checking time EL`: the run-time for the MEC analysis in s, if our algorithm is generally applied, regardless of the actual acceptance. 
    - `automaton size`: the number of states in the constructed automaton
    - `product size`: the number of states in the constructed product out of the automaton and the model
    - `automata translation time`: the time needed for the translation of the LTL formula into the deterministic automaton. The value is measured in seconds.
    - `acceptance sets`: The number of acceptance sets
- [`prism-el.tar.gz`]: Our extended version of PRISM v.4.4. For instruction to compile and run it see below.

Compiling and runnig PRISM
==========================

If you would like to run our benchmark, please install at the following:
- Please install Spot version 2.7.4. For instructions see [here](https://spot.lrde.epita.fr/install.html)
- Please install Rabinizer4. We used the version integrated in [owl 18.06](https://owl.model.in.tum.de/)
- a Java JDK >= 10 for executing Rabinizer4. If you just want compile and run PRISM, JDK >= 1.7 is necessary
- the usual build tools (C/C++ compiler, GNU Make)

At first step, please [download](prism.el.tar.gz) our PRISM implementation and unzip it via

    $ tar xzf prism-el.tar.gz


To compile PRISM, go to the `prism-el/prism/` subdirectory and execute `make`:

    $ cd prism-el/prism
    $ make

This will work on Mac OS X and Linux. 

If you have trouble compiling PRISM, please try downloading a source distribution from <http://www.prismmodelchecker.org/download.php> and try whether that works. We also refer to <http://www.prismmodelchecker.org/manual/InstallingPRISM/CommonProblemsAndQuestions>.

It might be necessary to set the `JAVA_DIR` variable so that the
Makefile can find the JDK installation (see the Makefile):

    $ make JAVA_DIR=xxx

Additionally, don't run make multi-threaded, i.e., with `-j x` for `x>1`.

As a result, you should have a working command-line version in

    prism-el/prism/bin/prism

and a GUI version in

    prism-el/prism/bin/xprism

PRISM can execute external tools to transform an LTL formula into a deterministic automaton. The according scripts can be found `prism-el/prism/etc/scripts/hoa/`. We used the scripts `hoa-rabinizer4-dgra-for-prism` and `hoa-ltl2tgba-dela-for-prism`. You might need to set the according path to Rabinizer (variable `RABINIZER`), to the JDK which should be used (variable `JAVA_HOME`), to autfilt of Spot (variable `AUTFILT`) and to ltl2tgba of Spot (variable `LTL2TGBA`).

PRISM accepts the following flags relevant to our paper:

- `-pf 'PROPERTY'`: Checks the property PROPERTY
- `-explicit`: Turns on the explicit engine of PRISM
- `-javamaxmem X`: Set a memory limit to X (we used 10g)
- `-ltl2datool SCRIPT`: Use the script SCRIPT to execute an external LTL-to-DA translation. If omitted, PRISM's internal ltl2dstar is used
- `-ltl2dasyntax SYNTAX`: Present the formula in SYNTAX form to the external script. Use `lbt` for `hoa-ltl2tgba-dela-for-prism` and `rabinizer` for `hoa-rabinizer4-dgra-for-prism`
- `-timeout N`: Set a time-out of N seconds (we used 1800)
- `-force-elcheck`: Enforces to use the Emerson-Lei check regardless of the acceptance

The model is taken as positional argument. As an example, to execute PRISM with Rabinizer and our Emerson-Lei emptiness check for the property `Pmax=? [((G F p1=0) | (F G p2!=0)) & ((G F p2=0) | (F G p3!=0))]`, execute:

    $prism-el/prism/bin/pris -pf 'Pmax=? [((G F p1=0) | (F G p2!=0)) & ((G F p2=0) | (F G p3!=0))]' mutual4.nm -explicit -ltl2datool prism-el/prism/etc/scripts/hoa/hoa-rabinizer4-dgra-for-prism  -ltl2dasyntax rabinizer -force-elcheck
