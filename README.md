
# Introduction

> Oh-ho-ho! Swirly Photoshop magic! I bet this thing could release some serious
> cackledemons!
> 
> &#x2013; Strong Bad

Anyone who has used a fully configured [Emacs](https://www.gnu.org/software/emacs/) install such as [Spacemacs](https://www.spacemacs.org/) or [Doom](https://github.com/hlissner/doom-emacs)
knows that Emacs can take a long time to initially boot. This isn't for a lack
of trying - in fact, the source code for Doom is half speed hacks and I highly
encourage anybody hacking on Emacs to take a look.

The truth is that Emacs is less a code editor and more of a **framework** for
**writing text-based applications**. I like to compare it to [Node.js](https://nodejs.org) in this
regard, which, while really good at being a webserver is no [NGINX](https://www.nginx.com/), but instead a
runtime where you can import lots of tiny pieces of functionality a la carte to
make your own webserver. Any one of these configurations does exactly this - it
installs packages from the internet, requires them and uses [Emacs Lisp](https://www.gnu.org/software/emacs/manual/html_node/eintr/) to create
a complete application. Spacemacs and Doom are built similarly, but feel
different because they **are** in fact meaningfully different.

Just as you don't want to load the JavaScript runtime for every request (this is
how non-Node webservers [used to do in the olden days](https://en.wikipedia.org/wiki/Common_Gateway_Interface) and trust me it was not
very good) you don't really want to load all of Emacs every time you open a file
either. It's less than ideal.

Luckily for us, Emacs can be ran [as a daemon](https://www.emacswiki.org/emacs/EmacsAsDaemon) and connected to via a client,
typically `emacsclient`. In Linux and [systemd](https://www.freedesktop.org/wiki/Software/systemd/) this works seamlessly - often you
can run `systemctl start --user emacs` and be off to the races. In OSX it's only
a little harder - you [copy-paste a plist file from the wiki](https://www.emacswiki.org/emacs/EmacsAsDaemon#toc8), pull some very
minor shenanigans and have a mostly working daemon.

In Windows it's a bit more of a mess. This is because the Windows abstractions
for processes and services are wildly divergent from those in nix-like
environments such as Linux and OSX. Init systems like systemd run and manage
normal process, meanwhile in Windows services are in fact [special programs](https://docs.microsoft.com/en-us/dotnet/framework/windows-services/walkthrough-creating-a-windows-service-application-in-the-component-designer) that
[expose an alternate interface](https://docs.microsoft.com/en-us/dotnet/api/system.serviceprocess?view=netframework-4.8) to that of the standard process. The upshot is
that any standard process that one wants to run as a Windows service needs to be
wrapped in one of these special programs, complete with bespoke abstractions for
process management, log management and so on.

Alternately, arbitrary processes may be [configured to start on user login](https://support.microsoft.com/en-us/help/4026268/windows-10-change-startup-apps).
Practically speaking, this is how most persistent applications are ran in
Windows, and in fact the [approach documented in the wiki](https://www.emacswiki.org/emacs/EmacsMsWindowsIntegration#toc8) does exactly this - it
uses a Powershell script to generate a very simple [batch file](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands) that cleans up
server state from the prior run and then spawns the Emacs daemon.

This works OK. However, there are some downsides. if the Emacs daemon crashes
for any reason, there's no signal other than `emacsclient` mysteriously not
working; any logs that come out of the Emacs daemon are lost to the aether; and
straightforward lifecycle actions that can be done casually with systemd (start,
top, restart, status) are very non-ergonomic.

Luckily, [PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview) has our back. PowerShell is a shockingly good language - it
performs admirably as a shell and yet scales to general purpose use, even being
capable of making proper [.NET classes](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_classes). More to the point, it's very good at
[managing processes](https://docs.microsoft.com/en-us/powershell/scripting/samples/managing-processes-with-process-cmdlets) and has [special support for background jobs](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/background-jobs), as well as being
able to [instantiate and manage tray icons](https://docs.microsoft.com/en-us/dotnet/api/system.windows.forms.notifyicon) via [Windows Forms](https://docs.microsoft.com/en-us/dotnet/framework/winforms/). All it needs is a
little elbow grease!

Cackledaemon is a PowerShell module that intends to be a solution for managing
such an instance of the Emacs daemon for Windows. It includes functions for
starting, stopping and restarting the Emacs daemon, functions and jobs for log
management and rotation, and a tray icon for a lil' point-and-click action and
notifications for if/when Emacs exits unexpectedly.


# License

Cackledaemon, much like Emacs, is licensed under the terms of the GPL v3 or
newer.

    # Copyright 2020 Josh Holbrook
    #
    # This file is part of Cackledaemon and not a part of Emacs.
    #
    # Cackledaemon is free software: you can redistribute it and/or modify
    # it under the terms of the GNU General Public License as published by
    # the Free Software Foundation, either version 3 of the License, or
    # (at your option) any later version.
    #
    # Cackledaemon is distributed in the hope that it will be useful,
    # but WITHOUT ANY WARRANTY; without even the implied warranty of
    # MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    # GNU General Public License for more details.
    #
    # You should have received a copy of the GNU General Public License
    # along with Cackledaemon.  if not, see <https://www.gnu.org/licenses/>.

