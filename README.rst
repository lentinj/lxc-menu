lxc-menu: Manage development containers
=======================================

Simple front-end to manage common tasks when you have a bunch of containers for different projects.

Designed to work in an unpriviledged user, with this script part of their profile, e.g.::

    echo './lxc-menu' > /home/fatcontroller/.profile

Configuring unpriviledged containers:

https://myles.sh/configuring-lxc-unprivileged-containers-in-debian-jessie/

Setting up new base containers
------------------------------

Generally I create ``base-(distro)`` containers first, with a relevant user and
some apt sources setup, then clone these for actual development boxes.

Creating new base images is done manually. As the fatcontroller::

    lxc-create -n base-bullseye -t download

Then start, attach and do some basic setup::

    apt update && apt upgrade
    apt install build-essential git openssh-server screen dialog ne curl

Add some external repostitories, e.g.

Node::

    DISTRO=bullseye
    VERSION=node_16.x
    KEYRING=/usr/share/keyrings/nodesource.gpg
    curl -fsSL https://deb.nodesource.com/gpgkey/nodesource.gpg.key | gpg --dearmor -o "$KEYRING"
    cat <<EOF > /etc/apt/sources.list.d/nodesource.list
    deb [signed-by=$KEYRING] https://deb.nodesource.com/$VERSION $DISTRO main
    deb-src [signed-by=$KEYRING] https://deb.nodesource.com/$VERSION $DISTRO main
    EOF

Yarn::

    KEYRING=/usr/share/keyrings/yarn.gpg
    curl -fsSL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor -o "$KEYRING"
    cat <<EOF > /etc/apt/sources.list.d/yarn.list
    deb [signed-by=$KEYRING] https://dl.yarnpkg.com/debian/ stable main
    EOF

CRAN::

    DISTRO=bullseye
    KEYRING=/usr/share/keyrings/cran.gpg
    # NB: CRAN instructions currently not updated, pulled key out of old installation
    # https://cloud.r-project.org/bin/linux/debian/
    curl https://raw.githubusercontent.com/lentinj/lxc-menu/master/cran.asc | gpg --dearmor -o "$KEYRING"
    cat <<EOF > /etc/apt/sources.list.d/cran.list
    deb [signed-by=$KEYRING] http://cloud.r-project.org/bin/linux/debian ${DISTRO}-cran40/
    EOF
