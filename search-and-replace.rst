Search and replace
##################
:date: 2010-11-26 05:23
:author: Laurence Noton
:category: Script

A command to search for a list of files matching an expression and
replacing part of the file name.  The example below searches for all
files matching \*.GIF and renames them to \*.gif.

for file in \`find . -name \*.GIF\` ; do mv $file \`echo $file \| sed
's/\\(.\*\\.\\)GIF/\\1gif/'\` ; done
