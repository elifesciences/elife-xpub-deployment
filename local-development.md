# Local Deployment Tesing

At times it may be necessary to change the way the deployment works, the most convenient way of doing this is to make changes locally. This avoids having to setup and wait for external services. This file contains some details on how to do this development locally.

Please also refer to the [Salt Stack](https://docs.saltstack.com/en/latest/) [elife-xpub-formula repo](https://github.com/elifesciences/elife-xpub-formula)

# Requirements

You will need installed `Vagrant` and `VirtualBox`

# Getting Started

Clone the repository [builder](https://github.com/elifesciences/builder) onto your local machine:
```
  git clone git@github.com:elifesciences/builder.git
```

In the root of the project folder run `vagrant up` and select elife-xpub. Alternatively you can supply this in the `PROJECT` environment variable:
```
   PROJECT=elife-xpub vagrant up
```
*NOTE* For the current documentation see the [builder repo]( https://github.com/elifesciences/builder/#vagrant)

# Testing the deployment
In order to test the deployment, please make changes to the [formula repo](https://github.com/elifesciences/elife-xpub-formula
) folder located under the root of the project folder here:
```
    cloned-projects/elife-xpub-formula
```

Check that the VM is running with:
```
    PROJECT=elife-xpub vagrant status
```

Then the deployment can be re-run with (if the VM is already running):
```
    PROJECT=elife-xpub vagrant provision
```

# Debugging

If you need to you can `ssh` into the VM with:
```
    PROJECT=elife-xpub vagrant ssh
```

When you are inside the VM, you will find that the [deployment repo](https://github.com/elifesciences/elife-xpub-deployment) is located in the folder:
```
    /srv/elife-xpub
```

If you need to change the default branch (normally master) of the deployment repo that is used for deploying. This can be done by using the command from within the VM:
```
    set_local_revision <branch of deployment>
```

Also deployments can be tested from within the VM using:
```
    sudo salt-call state.highstate
```

You can tear down all containers that have been run with docker-compose from the folder `/srv/elife-xpub` using:
```
    docker-compose down -v
```
*NOTE* The `-v` option will remove the volumes as well as the containers, which also means the database contents will also be deleted.