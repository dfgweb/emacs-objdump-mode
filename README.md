emacs-objdump-mode
==================

The objdump.el code here provides some simple support for
disassembling an executable or object file with GNU objdump and
browsing the result. (The "disassemble" command in Emacs is already
used for examining byte code.)

There are only a couple of interesting key bindings: "g" will re-run
the objdump command in case you've recompiled, and "s" will prompt for
a symbolic address, which can be of the forms "foo" or "foo+0x1234"
(the latter being common syntax in stack traces generated by the Linux
kernel), compute the actual hexadecimal address, and search for it in
the dump.

You can run "objdump -drl foo.o" yourself and save the results in a
text file with the initial line specifying "objdump" mode; in that
case, the "s" binding above will work, but "g" won't.

Oh, and if you run M-x objdump, it'll prompt for a file name, but will
override completion-ignored-extensions so you can complete on .o files
and such even if you can't normally.

Some code in ksyms.el may be useful when trying to look at Linux
kernel stack traces after a module has been unloaded. Use ksyms-parse
to parse the current buffer (or narrowed region) as /proc/kallsyms
content (before the module is unloaded), and save the result; later,
feed that data to update-symbols-in-stack-trace to scan the current
buffer (or narrowed region) and replace hex addresses with symbolic
ones when possible.

If you're trying to use kernel-based leak detection spanning the whole
time from loading your module to using it to unloading it (where some
objects aren't expected to be freed until cleanup gets done at unload
time), the allocation-time stack recorded may not be reported until
after you've unloaded it and removed some symbol table entries. This
code lets you fetch the symbol table while it's loaded, and fix up the
stack trace generated later.

It's not polished, and there are no interactive commands in this file.
