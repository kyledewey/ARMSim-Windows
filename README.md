# ASMSim2
ARM Simulator for .NET

Published as ARMSim#, a simulator of the ARM7TDMI architecture for .Net with a GUI interface.

## `hacked_batch` Branch ##

This branch is so I can run ARMSim from the command-line on a per-student basis.
While this functionality is already present, I discovered a number of bugs and problems with it:

- Bug: An exception is thrown leading to ARMSim crash if stdout is the same file as stderr
- Bug: Related to the bug above, if only one of stdout/stderr is provided, a crash occurs as internally both will attempt to use the same file
- Bug: The stdout parameter does not influence where STDOUT goes.
  The file will be opened, but nothing will ever be written to it, even if the program performs console output.
  stderr is similarly affected.
  This is because only the GUI portion sets up console output properly.
- Issue: By default, this loads a new set of `swi` instructions which are incompatible with the ones in the manual (which are now called "legacy").
  The legacy SWI instructions can only be enabled by changing the code.
- Issue: this now uses GAS (GNU `as`) for parsing, which is problematic as the syntax is different (especially with comments)

While I wanted to fix these problems right, it'd be a lot of extra work.
The bugs mentioned above are rooted in the fact that initialization is not straightforward for any of the key classes involved.
Constructors usually don't actually initialize objects, and the `Init` methods on the objects rarely actually completely initialize the object.
I/O is not abstracted very well; one would expect a single abstract output stream for writing which has subclasses for the console and for files, but this is not how the design works.
There are definitely abstractions here, but they are more complex than what I think is necessary.

For these reasons, I'm simply going to hack at it to make it do the following:

- Output is printed to the console
- Input files are stripped of components that makes them impossible to parse
