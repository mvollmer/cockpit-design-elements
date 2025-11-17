## Elements of Cockpit Design

This document continues the [Ideals
ofCockpit](https://cockpit-project.org/ideals.html) and aims to be more
concrete. And doesn't need to be as polished. Maybe some of the items
below can be added to the ideals.

This document is not about workflows or processes or tools like
Github, Figma, or Penpot.

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

It is even permissible (but only grudgingly so) to have a text field
for raw strings that are passed to the underlying technology and
interpreted there. For example, we have "Encryption options" in
Storage that are copied verbatim into /etc/crypttab, and the Teams in
Networking need to be configured with a JSON blob in a text area.
This is a bad UI, and we feel bad, but...

Cockpit makes it obvious when the user is leaving the "beaten path" by
keeping the non-guided parts separate, somehow.  [This needs to be
elaborated. Some dialogs have a "Advanced options" expander, but that
might not be enough when some of those options are real footguns, and
not just merely non-obviuous.  Maybe we should also have a standard
"hazard area" that is delimited with a yellow and black striped tape
like a real construction site.]

### SI vs IEC Unit prefixes

Cockpit uses the "human" SI unit prefixes "k", "M", "G", ... in
preference to the "computer" IEC unit prefixes "ki", "Mi", "Gi", ...

Cockpit might show numbers with either of the prefixes, and we might
change our preferences, but Cockpit will always use SI and IEC
correctly.  The "M" prefix will always mean a factor of 1000000, and
never 1048576.

Size of storage devices, network speeds, etc are shown with SI
prefixes. Amounts of RAM are shown with IEC prefixes. When space is
ample, a quantity should be shown with both SI and IEC prefixes and
the exact number without any prefix.  This is done to remove any
doubts over whether Cockpit gets the unit prefixes correct or not, and
to give the exact number without any of the rounding that Cockpit does
in other places to save space.

For example, disk capacity is shown as

    2.15 GB, 2 GiB, 2147483648 bytes

in the card with all the other verbose details of a disk.

### Patternfly

Cockpit uses Patternfly and generally follows Patternfly's design
guidelines.

Sometimes we fix (what we consider to be) bugs in PatternFly by adding
CSS to the PatternFly classes. This CSS is centralized in
`pkg/lib/patternfly`. For example, we think that it is a bug that
plain cards have round corners, and we fix that bug globally in
Cockpit.

But application code should not change the CSS of PatternFly
components just for their own purposes. For example, don't construct
what looks like a standard PatternFly page out of `Page`,
`PageSection`, and `Card` components in JavaScript, and then change
its layout completely by adding `display: grid` to it.

If there is no PatternFly component to achieve what you need,
implement a new component from scratch. The implementation should use
PatternFly classes and CSS variables to integrate with Patternfly
components, of course.

### Dialogs

Dialogs use a
[Form](https://www.patternfly.org/components/forms/form/design-guidelines)
component inside a
[Modal](https://www.patternfly.org/components/modal/design-guidelines)
component and should follow the relevant design guidelines.

Cockpit uses left-aligned labels.  [BUT SHOULD PROBABLY USE
TOP-ALIGNED ONES!]

Cockpit uses the Switch component only in situations where toggling
triggers an immediate change to the system, such as when switching
SELinux on or off.  In a dialog, where the change to the system
happens only once the dialog is submitted, Cockpit uses checkboxes
also to toggle between two different states.

When a dialog initially opens, the submit button is enabled and no
input field validation is done while the user is filling out the
dialog.

Once the submit button is clicked for the first time, input fields are
validated and if there are errors, the submit button is disabled and
the errors are shown. The dialog is scrolled to show the first
validation error at this point.

From that point on, the dialog does "online" input field validation on
each change to the input fields, such as after each key press in a
text input. Once the user has managed to get rid of all validation
errors, the submit button is enabled again.

(This avoids shouting "error, error, error" at the user when the
dialog opens in a state that is not ready for submission, but also
gives immediate feedback when correcting errors. It is also consistent
with dialogs that are prefilled and ready to be submitted immediately
after opening, something that the Patternfly guidelines overlook.)

[A dialog with multiple actions might have different validation rules
for each of the actions. For example "Format and mount" needs a
non-empty mount point, but "Format only" is happy with an empty
one. This needs to be thought through.]

Submitting the dialog successfully starts the action, disables the
submit button, and puts a spinner in it. Progress messages can be
shown next to the submit and cancel buttons.

While the opration is ongoing, all input fields are disabled as well.
[good idea?]

The cancel button now has the job of cancelling the ongoing operation
and should be disabled if that is not implemented or possible.  When
the operation finishes successfully, the dialog is closed.

When the operation fails, the error is shown in the dialog (at the
top) and everything is enabled again.

Operations that can fail in the middle and then leave the system in a
state where the dialog doesn't make sense anymore should be
avoided. If necessary, Cockpit should clean up itself and restore the
state to what it was before the operation was started, or silently
adjust the operation to skip the parts that have already been done.

### The standard Cockpit Page

[Something about the top-level of a Cockpit page. It's a Patternfly
Page but without any of the masthead and sidebar stuff, since that is
all already handled by the Shell. We probably should have a
CockpitPage component that abstracts away the correct use of the
Patternfly Page component, and also imports all the necessary CSS
overrides and additions that Cockpit needs. Maybe also include a
WithDialogs in that? Inside the CockpitPage, you can have your
standard PageSections.]

[Also something about PageSections, and whether or not to use plain
Cards for stuff, and how to get scrolling right when some parts of
your page should stay in place. Maybe have "header" and "footer" props
on CockpitPage to implement those things. Or CockpitHeader and
CockpitFooter with "sticky" props?]

### The standard Cockpit Card

[Something about a card with a optional description list at the top
and a optional table at the bottom, and buttons and a kebab. If your
object can be presented with that, it's fine to use it.]

### Tables and data lists

[More about tables, data lists, expander rows, putting actions into
tables.]

[No buttons in table rows, all actions in a kebab.]

[No whole row hovering and clicks, navigate with links in the first
column.]
