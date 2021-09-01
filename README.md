mrsync(1)                                                              mrsync(1)



NAME
       mrsync - minimalistic version of rsync

SYNOPSIS
       mrsync [OPTION]... SRC [SRC]... DEST

       mrsync [OPTION]... SRC [SRC]... [USER@]HOST:DEST

       mrsync [OPTION]... SRC [SRC]... [USER@]HOST::DEST

       mrsync [OPTION]... SRC

       mrsync [OPTION]... [USER@]HOST:SRC [DEST]

       mrsync [OPTION]... [USER@]HOST::SRC [DEST]

DESCRIPTION
       mrsync is a program that behaves in much the same way that rsync does, but
       has less options. 

GENERAL
       mrsync copies files either to or from a remote host, or locally  on  the
       current  host  (it  does  not  support copying files between two remote
       hosts).

       There are two different ways for mrsync  to  contact  a  remote  system:
       using  ssh or contacting an mrsync daemon directly via TCP.  The  
       remote-shell  transport is used whenever the source or destination path 
       contains a single colon (:) separator after a host specification.
       Contacting  an  mrsync daemon directly happens when the source or 
       destination path contains a double colon (::) separator after a host 
       specification.
       
       As a special case, if a single source arg is specified without a desti-
       nation, the files are listed in an output format similar to "ls -l".

       As expected, if neither the source or destination path specify a remote
       host, the copy occurs locally (see also the --list-only option).


SETUP
       See the file README for installation instructions.

       Once  installed,  you  can use mrsync to any machine that you can access
       via a remote shell (as well as some that you can access using the mrsync
       daemon-mode  protocol).   

       Note  that  mrsync  must be installed on both the source and destination
       machines.


USAGE
       You use mrsync in the same way you use rcp. You must  specify  a  source
       and a destination, one of which may be remote.

       Perhaps the best way to explain the syntax is with some examples:

              mrsync -t *.c foo:src/


       This would transfer all files matching the pattern *.c from the current
       directory to the directory src on the machine foo. If any of the  files
       already  exist on the remote system then the mrsync remote-update proto-
       col is used to update the file by sending only the differences. See the
       tech report for details.

              mrsync -av foo:src/bar /data/tmp


       This would recursively transfer all files from the directory src/bar on
       the machine foo into the /data/tmp/bar directory on the local  machine.
       The  files  are  transferred in "archive" mode, which ensures that
       permissions are preserved  in  the transfer.  

              mrsync -av foo:src/bar/ /data/tmp


       A trailing slash on the source changes this behavior to avoid  creating
       an  additional  directory level at the destination.  You can think of a
       trailing / on a source as meaning "copy the contents of this directory"
       as  opposed  to  "copy  the  directory  by name", but in both cases the
       attributes of the containing directory are transferred to the  contain-
       ing  directory on the destination.  In other words, each of the follow-
       ing commands copies the files in the same way, including their  setting
       of the attributes of /dest/foo:

              mrsync -av /src/foo /dest
              mrsync -av /src/foo/ /dest/foo


       You  can  also  use mrsync in local-only mode, where both the source and
       destination don't have a ':' in the name. In this case it behaves  like
       an improved copy command.

CONNECTING TO AN MRSYNC DAEMON
       It  is  also possible to use mrsync without a remote shell as the trans-
       port.  In this case you will directly connect to a remote mrsync daemon,
       typically  using  TCP port 10873.  (This obviously requires the daemon to
       be running on the remote system, so refer to the STARTING AN MRSYNC DAE-
       MON TO ACCEPT CONNECTIONS section below for information on that.)

       Using  mrsync  in  this  way is the same as using it with a remote shell
       except that:


       o      you either use a double colon :: instead of a  single  colon  to
              separate the hostname from the path.

       o      if you specify no local destination then a listing of the speci-
              fied files on the remote daemon is provided.

       An example that copies all the files in a remote directory named "src":

           mrsync -av host::src /dest


STARTING AN MRSYNC DAEMON TO ACCEPT CONNECTIONS
       In order to connect to an mrsync daemon, the remote system needs to have
       a daemon already running (or it needs to have configured something like
       inetd to spawn an mrsync daemon for incoming connections on a particular
       port).

       If  you're  using  ssh tunneling for the transfer,
       there is no need to manually start an mrsync daemon.

OPTIONS SUMMARY
       Here is a short summary of the options available in mrsync. Please refer
       to the detailed description below for a complete description.

        -v, --verbose               increase verbosity
        -q, --quiet                 suppress non-error messages
        -a, --archive               archive mode; same as -rpt (no -H)
        -r, --recursive             recurse into directories
        -u, --update                skip files that are newer on the receiver
        -d, --dirs                  transfer directories without recursing
        -H, --hard-links            preserve hard links
        -p, --perms                 preserve permissions
        -t, --times                 preserve times
            --existing              skip creating new files on receiver
            --ignore-existing       skip updating files that exist on receiver
            --delete                delete extraneous files from dest dirs
            --force                 force deletion of dirs even if not empty
            --timeout=TIME          set I/O timeout in seconds
            --blocking-io           use blocking I/O for the remote shell
        -I, --ignore-times          don't skip files that match size and time
            --size-only             skip files that match in size
            --address=ADDRESS       bind address for outgoing socket to daemon
            --port=PORT             specify double-colon alternate port number
            --list-only             list the files instead of copying them
       -h   --help                  show this help


       mrsync  can also be run as a daemon, in which case the following options
       are accepted:

            --daemon                run as an mrsync daemon
            --address=ADDRESS       bind to the specified address
            --no-detach             do not detach from the parent
            --port=PORT             listen on alternate port number
        -h, --help                  show this help (if used after --daemon)



OPTIONS
       Many  of  the  command  line options  have  two  variants,  one short and 
       one long.  These are shown below, separated by commas. Some options only 
       have a long variant.  The '='  for  options  that take a parameter is 
       optional; whitespace can be used instead.


       --help Print a short help page  describing  the  options  available  in
              mrsync  and exit.  For backward-compatibility with older versions
              of mrsync, the help will also be output if you use the -h  option
              without any other args. -- fonctionne

       -v, --verbose
              This  option  increases  the amount of information you are given
              during the transfer.  By default, mrsync works silently. A single
              -v  will  give you information about what files are being trans-
              ferred and a brief summary at the end. Two -v  flags  will  give
              you  information  on  what  files are being skipped and slightly
              more information at the end. More than two -v flags should  only
              be used if you are debugging mrsync.

              Note that the names of the transferred files that are output are
              just  the  name of the file. At the single -v level of verbosity, 
              this does not mention when a file gets its attributes changed. -- fonctionne        

       -q, --quiet
              This  option  decreases  the amount of information you are given
              during the transfer, notably  suppressing  information  messages
              from  the remote server. This flag is useful when invoking mrsync
              from cron. -- pas implémentée


       --size-only
              Normally mrsync will not transfer any files that are already  the
              same  size  and  have the same modification time-stamp. With the
              --size-only option, files will not be transferred if  they  have
              the  same  size,  regardless  of  timestamp. This is useful when
              starting to use mrsync after using another mirroring system which
              may not preserve timestamps exactly. -- pas testée


       -r, --recursive
              This  tells  mrsync  to  copy  directories recursively.  See also
              --dirs (-d).

       -u, --update
              This  forces mrsync to skip any files which exist on the destina-
              tion and have a modified time that  is  newer  than  the  source
              file.   (If an existing destination file has a modify time equal
              to the source file's, it will be updated if the sizes  are  dif-
              ferent.) -- pas testée


       -d, --dirs
              Tell the sending  side  to  include  any  directories  that  are
              encountered.  Unlike --recursive, a directory's contents are not
              copied unless the directory name specified is "." or ends with a
              trailing  slash (e.g. ".", "dir/.", "dir/", etc.).  Without this
              option or the --recursive option, mrsync will skip  all  directo-
              ries it encounters (and output a message to that effect for each
              one).  If you specify both --dirs and  --recursive,  --recursive
              takes precedence. -- testée partiellement



       -p, --perms
              This  option  causes  the receiving mrsync to set the destination
              permissions to be the same as the source permissions.  (See also
              the  --chmod  option for a way to modify what mrsync considers to
              be the source permissions.) -- fonctionne 

              When this option is off, permissions are set as follows:


              o      Existing files (including  updated  files)  retain  their
                     existing  permissions.

              o      New files get their "normal" permission bits set  to  the
                     source file's permissions masked with the receiving end's
                     umask setting, and their special permission bits disabled
                     except  in the case where a new directory inherits a set-
                     gid bit from its parent directory.


              Thus,  when  --perms is disabled, mrsync's  behavior is the same 
              as that of other file-copy utilities, such as cp(1) and tar(1).

              In summary: to give destination files (both  old  and  new)  the
              source permissions, use --perms.  To give new files the destina-
              tion-default   permissions   (while   leaving   existing   files
              unchanged),  make  sure  that  the --perms option is off.

              Like in recent versions of rsync, the  preservation  of 
              the destination's setgid bit on newly-created directories 
              when --perms is off is supported by  mrsync.
              Older  rsync  versions  erroneously  preserved the three special
              permission bits for newly-created files when  --perms  was  off,
              while  overriding  the  destination's  setgid  bit  setting on a
              newly-created directory.


       --ignore-existing
              This  tells  mrsync  to skip updating files that already exist on
              the destination (this does not ignore  existing  directores,  or
              nothing would get done).  See also --existing. -- testée partiellement





       --timeout=TIMEOUT
              This option allows you to set a maximum I/O timeout in  seconds.
              If no data is transferred for the specified time then mrsync will
              exit. The default is 0, which means no timeout. 

       --blocking-io
              This tells rsync to use blocking I/O  when  launching  a  remote
              shell  transport.   It  defaults  to
              using  non-blocking  I/O.   (Note  that ssh prefers non-blocking
              I/O.) -- pas implémentée



       --list-only
              This  option will cause the source files to be listed instead of
              transferred.  This option is  inferred  if  there  is  a  single
              source  arg  and no destination specified, so its main uses are:
              (1) to turn a copy command that includes a destination arg  into
              a  file-listing command, (2) to be able to specify more than one
              local source arg (note: be sure to include the destination).
              Caution: keep in mind that a source arg with a wild-card is 
              expanded by the shell into multiple args, so it is never safe to 
              try to list such an arg without using this option.  For example:

                  mrsync -av --list-only foo* dest/ -- fonctionne 





SYMBOLIC LINKS
       Symbolic links are  not  transferred  at  all.   A  message
       "skipping non-regular" file is emitted for any symlinks that exist.
       -- j'ai ignoré les liens

DIAGNOSTICS
       mrsync occasionally produces error messages that may seem a little cryp-
       tic. The one that seems to cause the most confusion is  "protocol  ver-
       sion mismatch -- is your shell clean?".

       This  message is usually caused by your startup scripts or remote shell
       facility producing unwanted garbage on the stream that mrsync  is  using
       for  its  transport.  The  way  to diagnose this problem is to run your
       remote shell like this:

              ssh remotehost /bin/true > out.dat


       then look at out.dat. If everything is working correctly  then  out.dat
       should  be  a zero length file. If you are getting the above error from
       mrsync then you will probably find that out.dat contains  some  text  or
       data.  Look  at  the contents and try to work out what is producing it.
       The most common cause is incorrectly configured shell  startup  scripts
       (such  as  .cshrc  or .profile) that contain output statements for non-
       interactive logins.


EXIT VALUES
       0      Success

       1      Syntax or usage error

       3      Errors selecting input/output files, dirs

       5      Error starting client-server protocol

       10     Error in socket I/O

       11     Error in file I/O

       12     Error in mrsync protocol data stream

       20     Received SIGUSR1 or SIGINT

       21     Some error returned by waitpid()

       23     Partial transfer due to error

       24     Partial transfer due to vanished source files

       30     Timeout in data send/receive


SEE ALSO
       rsync(1)


BUGS
       - Works fine for files under 100MB, reading/writing process works partially
         through PDFs, had trouble testing, no fix found yet
       - When syncing large files, transfer can stop for no obvious reason
       - Prioritize transfers originating from same directory (i.e 'mrsync.py A/B A/C')
       - '--server' not finished



INTERNAL OPTIONS
       The option --server is used  internally  by  mrsync,  and
       should  never  be  typed  by  a  user under normal circumstances.



CREDITS
       A  WEB site is available at http://rsync.samba.org/.  The site includes
       an FAQ-O-Matic which may cover  questions  unanswered  by  this  manual
       page.

AUTHORS
       This mrsync has been written by Ahmed G.

                                  L2 info math-info 2021              mrsync(1)