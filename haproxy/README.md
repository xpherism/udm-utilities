# Run haproxy on your UDM

## Features

1. Load balance services on your UDM, because why not?.
2. Persists through reboots and firmware updates.

## Requirements

1. You have successfully setup the on boot script described [here](https://github.com/unifi-utilities/unifios-utilities/tree/main/on-boot-script)
2. You have to have services you want to load-balance, an example would be a multi-master k3s cluster.

## Steps

1. Pull your image with `podman pull docker.io/library/haproxy`.

1. Copy [50-haproxy.sh](./50-haproxy.sh) to `/mnt/data/on_boot.d/50-haproxy.sh`.

1. Choose network configuration - You can run either on the host network or on a seperate docker network. Running on the host network is easier but does mean you can't clash with the ports already in use on the UDM. 
    1. If you want to run on the host network
        1. You don't have to do anything extra to run on the host network all the instructions / scripts assume this setup.
    1. If you want to run on a custom docker network do the following:
        1. Setup the network - there are some instructions in the Customizations setting of the pihole instructions: https://github.com/unifi-utilities/unifios-utilities/tree/main/run-pihole#customizations
        1. Copy [21-haproxy.conflist](./21-haproxy.conflist) to `/mnt/data/podman/cni/`  and update its values to reflect your environment.
        1. Execute the `/mnt/data/on_boot.d/05-install-cni-plugins.sh` script to create the network.
        1. Edit `/mnt/data/on_boot.d/50-haproxy.sh` and change `--net=host` to `--network haproxy`
1. Create a persistant directory and config for haproxy to use:

    ```sh
    mkdir -p /mnt/data/haproxy
    touch /mnt/data/haproxy/haproxy.cfg
    ```

1. Add your config to `/mnt/data/haproxy/haproxy.cfg`. Each configuration is unique, so check out some resouces like [haproxy.com](https://www.haproxy.com/documentation/hapee/latest/configuration/config-sections/) for basics.
1. Run `/mnt/data/on_boot.d/50-haproxy.sh`

## Upgrading Easily (if at all)

1. Edit [update-haproxy.sh](./update-haproxy.sh) to use the same command you used at installation (if changed). If you added your own network config ensure you change the `--net=host` to `--network haproxy`
2. Copy the [update-haproxy.sh](./update-haproxy.sh) to `/mnt/data/scripts`
3. Anytime you want to update your installation, simply run `/mnt/data/scripts/update-haproxy.sh`
