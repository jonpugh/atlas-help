---
title: "Box Release Lifecycle"
---
# Box Release Lifecycle

One of the best features of Atlas by HashiCorp is how easy it makes the workflow
and process for creating new versions of boxes and releasing them to your
team. Atlas by HashiCorp also lets you create new versions of boxes without
releasing them or without Vagrant seeing the update. This lets you prepare
a box for release slowly.

First, some terminology: when a box version is _released_, Vagrant is able to
add and use that box version. If a version of a box is being created in
Atlas by HashiCorp but is not yet released, then Vagrant will not see it, and
we say that the box version is _unreleased_.

There are multiple components of a box:

* The box itself, comprised of the box name and description.

* One or more box versions.

* One or more providers for each box version.

A box can only be released if it has at least one of each component: a
box, a version, and a provider. When [creating a box](/help/boxes/create),
Atlas by HashiCorp guides you through creating all of these initial components.
