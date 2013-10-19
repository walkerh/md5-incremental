md5-incremental
===============

Tool to incrementally read a growing file, computing a md5 checksum.

## Motivation ##

We needed to verify the copy of files with sizes measured in hundreds of
gigabytes. The naive approach would be to copy the file and then run md5sum.
This tool allows computing the md5 checksum in parallel with the file copy.

## Operation ##

Will continuously (every 0.5 secs) look for the existence of a "control file",
which signals that the input file is complete. At that point, md5incremental
will send the md5 checksum to standard output and exit.

Typical invocation (in a separate shell):

    md5incremental input_file control_file

Client code will typically trigger execution halt and md5 output like this:

    touch control_file

## Example ##

Copy a large file

    (dd if=source-file of=dest-file; touch dest-file-done) &
    md5incremental dest-file dest-file-done > dest-file.md5

While the copy runs in the background, md5incremental tracks the contents
of the file and waits for dest-file-done to be created. Once dest-file-done
is created, md5incremental closes the input file and writes the md5
checksum to standard output, which is redirected to dest-file.md5.
