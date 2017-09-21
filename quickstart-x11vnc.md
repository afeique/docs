**Created**: July 17, 2017

# Quick and Dirty `x11vnc`

       xrandr --fb 1024x768
       x11vnc -display :0 -rfbport 5900 -auth guess -shared -forever -bg -loop -xvnc

       -auth file

              Set the X authority file to be file, equivalent to  setting  the
              XAUTHORITY environment variable to file before startup.  Same as
              -xauth file.  See Xsecurity(7) , xauth(1)  man  pages  for  more
              info.

              Use  '-auth  guess'  to  have x11vnc use its -findauth mechanism
              (described below) to try to guess the  XAUTHORITY  filename  and
              use it.

              XDM/GDM/KDM:  if you are running x11vnc as root and want to find
              the XAUTHORITY before anyone has logged into an X  session  yet,
              use:  x11vnc -env FD_XDM=1 -auth guess ...  (This will also find
              the XAUTHORITY if a user is already logged into the X  session.)
              When  running  as  root,  FD_XDM=1  will be tried if the initial
              -auth guess fails.

       -N

              If the X display is :N, try to set the VNC display to also be :N
              This  just  sets  the -rfbport option to 5900+N The program will
              exit immediately if that port is not available.  The  -N  option
              only  works  with  normal  -display  usage, e.g. :0 or :8, -N is
              ignored in the -display WAIT:..., -create, -find,  -svc,  -redi‐
              rect, etc modes.

       -autoport n

              Automatically  probe  for  a  free  VNC port starting at n.  The
              default is to start probing at 5900.  Use this to stay away from
              other VNC servers near 5900.

       -rfbport str

              The  VNC  port to listen on (a LibVNCServer option), e.g.  5900,
              5901, etc.  If specified as "-rfbport PROMPT"  then  the  x11vnc
              -gui is used to prompt the user to enter the port number.


       -viewonly

              All VNC clients can only watch (default off).

       -shared

              VNC  display is shared, i.e. more than one viewer can connect at
              the same time (default off).

       -once

              Exit after the first successfully connected viewer  disconnects,
              opposite of -forever. This is the Default.

       -forever

              Keep  listening for more connections rather than exiting as soon
              as the first client(s) disconnect. Same as -many

              To get the standard non-shared VNC behavior where when a new VNC
              client connects the existing VNC client is dropped use:  -never‐
              shared -forever   This method can also be used to guard  against
              hung TCP connections that do not go away.

       -loop

              Create  an  outer loop restarting the x11vnc process whenever it
              terminates.  -bg and -inetd are ignored in  this  mode  (however
              see -loopbg below).

              Useful  for  continuing  even  if  the  X  server terminates and
              restarts (at that moment the process  will  need  permission  to
              reconnect to the new X server of course).

              Use,  e.g.,  -loop100  to  sleep 100 millisecs between restarts,
              etc.  Default is 2000ms (i.e. 2 secs) Use,  e.g.  -loop300,5  to
              sleep 300 ms and only loop 5 times.

              If  -loopbg  (plus  any numbers) is specified instead, the "-bg"
              option is implied and the mode approximates  inetd(8)  usage  to
              some  degree.  In this case when it goes into the background any
              listening sockets (i.e. ports 5900, 5800)  are  closed,  so  the
              next  one  in  the loop can use them.  This mode will only be of
              use if a VNC client  (the  only  client  for  that  process)  is
              already  connected  before the process goes into the background,
              for example, usage of -display WAIT:.., -svc, and  -connect  can
              make use of this "poor man's" inetd mode.  The default wait time
              is 500ms in this mode.  This usage could use useful:   -svc  -bg
              -loopbg

       -usepw

              If no other password method was supplied on  the  command  line,
              first  look for ~/.vnc/passwd and if found use it with -rfbauth;
              next, look for ~/.vnc/passwdfile and use  it  with  -passwdfile;
              otherwise,   prompt   the   user   for   a  password  to  create
              ~/.vnc/passwd and use it with the -rfbauth option.  If  none  of
              these succeed x11vnc exits immediately.

       -storepasswd pass file

              Store  password pass as the VNC password in the file file.  Once
              the password is stored the program exits.  Use the password  via
              "-rfbauth file"

              If  called with no arguments, "x11vnc -storepasswd", the user is
              prompted  for  a  password  and  it  is  stored  in   the   file
              ~/.vnc/passwd.   Called with one argument, that will be the file
              to store the prompted password in.

       -nopw

              Disable the big warning message when you use x11vnc without some
              sort of password.

       -create

              First try to find the user's display using FINDDISPLAY, if  that
              doesn't  succeed  create  an X session via the FINDCREATEDISPLAY
              method.  This is an alias for "-display  WAIT:cmd=FINDCREATEDIS‐
              PLAY-Xvfb".

              Note:  if  a  -display  occurs later on the command line it will
              override the -create setting.

              SSH NOTE: for both -find and -create you can (should!)  add  the
              "-localhost" option to force SSH tunnel access.

       -xdummy

              As in -create, except Xdummy instead of Xvfb.

       -xvnc

              As in -create, except Xvnc instead of Xvfb.

       -xdummy_xvfb

              Sets WAIT:cmd=FINDCREATEDISPLAY-Xdummy,Xvfb
