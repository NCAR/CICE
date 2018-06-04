Use the following commands to use sphinx to auto-build the html documentation from the main
CICE/doc directory:

  >make clean
  >make html

Only the doc/source files (*.rst) are stored on the master
branch.

The build/html files should be copied to the appropriate subdirectory
https://github.com/NCAR/CICE/ gh-pages orphan branch
commited to that branch separately.
