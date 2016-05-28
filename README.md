% MParallel
% Created by LoRd_MuldeR &lt;<mulder2@gmx>&gt; &ndash; check <http://muldersoft.com/> for news and updates!


# Introduction #

**MParallel** is a batch processor with *multi-threading* support, i.e. it will run multiple tasks *in parallel*. This can be very useful, not only, to take full advantage of multi-processor (multi-core) machines.


# Synopsis #

**MParallel** can be invoked in three different forms:

    MParallel.exe [options] <command_1> : <command_2> : ... : <command_n>
    MParallel.exe [options] --input=commands.txt
    GenerateCommands.exe [parameters] | MParallel.exe [options] --stdin

The *first* form takes commands directly from the command-line, delimited by colon (`:`) characters. The *second* from reads commands from a text file, line by line. And the *third* form reads commands from the standard input stream (stdin), which is typically used to process the output from another program using a pipe (`|`). The three forms can be combined freely.


# Examples

This basic example uses **MParallel** to run multiple "ping" commands in parallel:

    MParallel.exe ping.exe -n 16 google.com : ping.exe -n 16 fsf.org : ping.exe -n 16 gnu.org : ping.exe -n 16 github.com

A slightly more advanced example, using a command pattern to express the above command-line more elegantly:

    MParallel.exe --pattern="ping.exe -n 16 {{0}}" google.com : fsf.org : gnu.org : github.com

Now let's read the output of the "dir" command to copy all "&ast;.jpg" file to "&ast;.jpg.v2":

    dir /b *.jpg | MParallel.exe --shell --stdin --auto-quote --pattern="copy {{0}} {{0}}.v2"

Note that, in the last example, we need to use `--shell` option, because `copy` is a built-in shell function, **not** a program.


# Options

The following **MParallel** options are currently available:

* `--count=<N>`  
  Run at most **N** instances in parallel. MParallel will start **N** commands in parallel, provided that there are (at least) **N** commands in the queue. If there are *less* than **N** commands in the queue, it will start as many commands in parallel as there are in the queue. If there are *more* than **N** commands in the queue, initially MParallel will start only **N** commands in parallel. Also, at each time that any of the running commands completes, it will start the next command. Note that **N** defaults to the number of available CPU cores, if *not* specified explicitly.

* `--pattern=<PATTERN>`  
  Generate commands from the specified **PATTERN** string. If a pattern has been specified, the commands given on the command-line, read from a file or read from the STDIN will *not* be executed "as-is". Instead, they will be interpreted as parameters for the given **PATTERN** string. For this purpose, the **PATTERN** string should contain placeholders in the ``{{N}}`` form. Placeholders of that from will be replaced by the **N**-th command-token. Note that the placeholder indices **N** are *zero-based* (i.e.use  ``{{0}}``, ``{{1}}``, ``{{2}}`` and so on). See also the `--auto-quote` option.

* `--separator=<SEP>`  
  Set the command separator to **SEP**. The separator string is used to delimit the distinct commands, when they are passed to MParallel on the command-line. By default, a single colon character (`:`) is used as separator, but any suitable character sequence may be specified here. Note that **SEP** is *not* used for reading commands from a file or from the STDIN. When reading from a file or from the STDIN, there must be one command per line.

* `--input=FILE`  
  Read additional commands from the specified **FILE**. The specified **FILE** needs to be a plain text file, containing one command per line. If the file contains any *non*-US-ASCII, it is expected to be in *UTF-8* text encoding.

* `--stdin`  
  Read additional commands from **STDIN** stream. The data passed on the STDIN needs to be plain text, containing one command per line. If the stream contains any *non*-US-ASCII, the *UTF-8* text encoding is assumed.

* `--auto-quote`  
  Automatically wrap all tokens that contain any whitespace characters in quotation marks. This applies to the expansion of placeholders, when the `--pattern` option is used. For example, if the **N**-th command token contains `foo bar`, then `{{N}}` will be replaced by `"foo bar"` instead of `foo bar`. This option has *no* effect, if `--pattern` is *not* used.

* `--shell`  
  Start each command inside a new sub-shell (cmd.exe). Running each command in a new sub-shell implies a certain overhead, which is why this behavior is *disabled* by default. However, you *must* use this option, if your command uses any *built-in* shell functions, such as `echo`, `dir` or `copy`. You also  *must* use this option, if your command contains any shell operators, such as the pipe operator (`|`) or one of the redirection operators (`>`, `<`, etc).

* `--timeout=<TIMEOUT>`  
  Kill processes after **TIMEOUT** milliseconds. By default, each command is allowed to run for an infinite amount of time. If this option is set, a command will be *aborted* if it takes longer than the specified timeout interval. Note that (by default) if a command was aborted due to timeout, other pending commands will still get a chance to run.

* `--abort`  
  Abort batch, if any command failed to execute. By default, if any command failed, e.g. because the process could *not* be created or because it returned a *non-zero* exit code, other pending commands will still get a chance to run. If this option is set, the whole batch (queue) will be aborted, as soon as one command has failed.

* `--silent`  
  Disable all textual messages, also known as "silent mode". Note that *fatal* error messages may still appear under some circumstances. Also note that this option is mutually exclusive with the `--trace` option.

* `--trace`  
  Enable more diagnostic outputs. This will print, e.g., the full command-line and the exit code for each task, which can be helpful for debugging purposes. Note that this option is mutually exclusive with the `--silent` option.

* `--help`  
  Print the help screen, also known as "manpage".


# License Terms #


**MParallel** is released under the *GNU General Public License*, Version 2.

    MParallel - Parallel Batch Processor
    Copyright (C) 2016 LoRd_MuldeR <mulder2@gmx.de>. Some rights reserved.
    
    This program is free software; you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation; either version 2 of the License, or
    (at your option) any later version.
    
    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.
    
    You should have received a copy of the GNU General Public License along
    with this program; if not, write to the Free Software Foundation, Inc.,
    51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.

**<http://www.gnu.org/licenses/gpl-2.0.html>**


&nbsp;  

e.o.f.