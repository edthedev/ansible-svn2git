Ansible recipe for svn2git
===========================

Prerequisites
--------------
This script requires:
 * Ansible
 * Vagrant
 * A virtual machine manager such as(VirtualBox or VMWare Workstation.

Bring up the SVN VM.
---------------------
::

  git clone git@github.com:edthedev/ansible-svn2git.git
  vagrant up
  vagrant provision
  vagrant ssh

Do the import.
---------------
See https://github.com/nirvdrum/svn2git#usage

Here's the steps that worked for me.

Start the authors file. Do not include trunk in the repo path. svn2Git will need to see everything, trunk, branches, tags.::

  svn log --quiet https://path/to/repo | grep -E "r[0-9]+ \| .+ \|" | cut -d'|' -f2 | sed 's/^ //' | sort | uniq >> author_netids.txt
  # Start the authors file.
  cp author_netids.txt authors.txt

Update authors.txt into the form svn2git expects.::

  vi authors.txt

  jcoglan = James Coglan <jcoglan@never-you-mind.com>
  stnick = Santa Claus <nicholas@lapland.com>

Migrate. Do not include trunk in the repo path. svn2Git will need to see everything, trunk, branches, tags.
It will take time.::

  svn2git http://svn.example.com/path/to/repo --authors ~/authors.txt --branches branches --tags tags --trunk trunk

You should see a message like this, updating the revision number periodically.::

  This may take a while on large repositories
  Checked through r13800

If this fails, you may want to export a smaller portion of the repository.::

  svn2git http://svn.example.com/path/to/repo --authors ~/authors.txt --branches branches --tags tags --trunk trunk --revision 31457

If you have questions or need a more nuanced approach to svn2git, see the FAQ:https://github.com/nirvdrum/svn2git#faq

Verify
-------
svn2git should have converted trunk to master, SVN branches to Git branches, and SVN tags to Git tags.::

  >git branch
    1.3.final
    2.5-bugfix
    develop-2.0
  * master
    ...etc...

  >git tag
    1.3.final
    release-1.0
    release-1.1
    release-1.1_bugfix-1.0
    release-1.2
    release-1.3
    ...etc...

Push you exported Git repository somewhere permanent.
------------------------------------------------------

Create a new bare repository on the remote Git server.::

    ssh your-git-server
    git init --bare AppName.git

Back inside your VM, add the new remote bare repository as a remote named origin.::

    git remote add origin https://remote/git/AppName.git

If you're using Git 1.7 or earlier, you will likely need to update .git/config to add your username to the remote.
:: 
    [remote "origin"] 
            url = https://username@git-server.git

Push the master to the remote server.::
    git push -u origin master 

Push tags to the remote server.::
    git push --tags

Push branches to the remote server.::
    git push --all

Now you can clone to the development workstation that you actually intend to work from.::

    git clone https://remote/git/AppName.git
