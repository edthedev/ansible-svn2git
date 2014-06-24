Ansible recipe for svn2git
===========================

Bring up the SVN VM.
---------------------
::

  vagrant up
  vagrant provision
  vagrant ssh

Do the import.
---------------
See https://github.com/nirvdrum/svn2git#usage

Here's the steps that worked for me.

Start the authors file. Do not include trunk in the repo path. svn2Git will need to see everything, trunk, branches, tags.

::
  svn log --quiet https://path/to/repo | grep -E "r[0-9]+ \| .+ \|" | cut -d'|' -f2 | sed 's/^ //' | sort | uniq >> author_netids.txt
  # Start the authors file.
  cp author_netids.txt authors.txt

Update authors.txt into the form svn2git expects.

::
  vi authors.txt

  jcoglan = James Coglan <jcoglan@never-you-mind.com>
  stnick = Santa Claus <nicholas@lapland.com>

