+++
title = "Jupyter Notebooks and the Remote Kernel"
date = "2020-01-10"
summary = "My experimentations with Jupyter"
+++

Before I get into any tech, some background into my current situation.
At my home there's a quite powerful gaming PC that I've set up to run a [Jupyter
Notebook](https://jupyter.org) server when running the installed Linux distro.
Unfortuanately, it is also used as a gaming machine so it sometimes turns off
and switches to windows. Since it isn't my computer I don't expect to have to be
given any warning to this and I can expect it to happen when I am not home.

Now, this machine has been great use for me studying some machine learning
techniques but it's slight unreliability is sometimes frustrating, so I've set
out to come up with a system that will use this machine if it's available,
otherwise settle with my cheap remote server that will still get the job done,
just taking much longer in the process.

Let's document my current setup. The PC has a startup script that runs a jupyter
server as well as an autossh reverse ssh session to my remote server (hostname
tesla, so I shall refer to it as that from now on). Tesla then has a simple
NGINX reverse proxy setup which handles the websockets for the kernels and the
terminals and correctly maintains a jupyter session. This all works seamlessly.
If the PC is running, it establishes the ssh session to Tesla, which then
forwards any data to the PC if it's available.

--------------------------------------------------------------------------------

So, where to go from here? I have a few ideas. My first idea is to setup the
jupyter notebook on Tesla instead. Then have some custom kernels installed on
that notebook that can control two kernels simultaneously, one on Tesla itself
and one on the PC if it's online. That way, I get the reliability of keeping
everything on Tesla, which I can access anywhere at anytime, and the speed of
our PC if it's available.

There are a few problems to figure out first though. First I need to have files
synced between both machines. If I make a checkpoint of my network and it's
using the PC's kernel, Tesla needs to be made aware and sync that file.
Similarly, if the PC comes online it needs to sync with any new files that may
be on the server. 

Another issue is regarding custom kernels. I want to be able to install a new
kernel on Tesla, let's say it's a virtualenv, and have the remote kernel show up
in the list, as well as installing it on the PC to be used. I would like to
avoid having any custom scripts to do this and have it all be automatic but I
don't see this being straight forward. I'm going to have to investigate more
about how to configure a jupyter server.

Finally, how do I sync the two kernels. I have a basic idea but I'm not sure if
it's the most effective or even possible. Let's say we have three kernels, A, B
and R. A runs on Tesla, B runs on the PC and R is a psuedo kernel which
maintains the sync between them. A very language/kernel specific solution might
be to send the code to B to run, then get the values of the current variables
that were updated in that code snippet, and send a very simple instruction to A
of those direct assignments. There might be a way to just get a dump of the
kernel's memory and keep those synced however (Which would still cause problems
in the events of open filehandles and such). I'll have to investigate further.

--------------------------------------------------------------------------------

Currently this is just an idea and a very specific use case. I am no where near
to releasing anything from this as I have barely just started. However, I will
be updating this blog post as I learn more about how jupyter and the kernels
work. I've been using the new firefox websockets inspector tools to great use. I
hope to find some good documentation about how the kernels work but so far it
seems I have to just read source code... In which case I hope I can start
documenting further just what kernels can do
