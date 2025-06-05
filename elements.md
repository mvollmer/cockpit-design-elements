## Elements of Cockpit Design

This document continues the [Ideals of
Cockpit](hts://cockpit-project.org/ideals.html) and aims to be more
concrete. And doesn't need to be as polished. Maybe some of the items
below can be added to the ideals.

### No hiding of technology, no draining the swamp

Cockpit provides access to the technologies available in a Linux
distribution. It does not hide these technologies, but it makes them
easier to use than on the command line.

For example, for networking there are both "bridges" and "teams" and
Cockpit does not make a choice between them.  It presents "bridges"
and "teams" equally.  Cockpit says "LVM2" and "Stratis" and "xfs" and
"btrfs" in its UI.

One could argue that the OS really should only have one way of
implementing bridging functionality, and only one all-powerful volume
manager filesystem.  But Linux is not like that, and Cockpit by itself
does not try to change it. It is not a goal of Cockpit to "drain the
swamp".

### Guided and Non-Guided

Cockpit guides the user towards the most common choices and best
practices, but does not refuse to provide access to options and
actions that are only needed in very specific situations.

The guided portion of the UI must assume that the user is using
Cockpit to learn about and experiment with the technology. Options and
actions should make sense in plain language and short explanations
should help keep people on the right track.

The non-guided portion of the UI can assume that the user is not using
Cockpit to learn about the technologies, but that they have a big
manual open or a expert on a video call that tells them exactly what
to do.  The manual or expert know the technology, but not necessarily
how Cockpit provides access to it.

These non-guided parts of the UI exist because there is no hope to
make the corresponding parts of the technology make sense without long
explanations, diagrams, and maybe even courses and certifications.
Still, once you have done the learning or have gotten the right hint,
it is much easier to get the task done (and remember how you did it
for the next time) when you do it in Cockpit rather than on the
command line.

Cockpit makes it obvious when the user is leaving the "beaten path" by
keeping the non-guided parts separate, somehow.
