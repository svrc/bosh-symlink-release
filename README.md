# BOSH symlinker release for Tanzu or BOSH VMs

## What does this do?

If you use 3rd party software installed outside of the BOSH lifecycle, e.g. Azure policy or VM extensions to install OMS agent on BOSH-created VMs, this will relocate binaries & logs so they don't blow up your root partition, which is typically restricted to a fixed 3 GB size in BOSH, in favor of the ephemeral partition under `/var/vcap/data`.


## How do I install it?

1. Open a shell prompt on a BOSH CLI with access to your bosh director, such as Ops Manager.
2. Export your BOSH credentials to the enviornment.  These can be accessed via the Ops Manager GUI -> BOSH Director Tile -> Credentials Tab -> Bosh Commandline Credentials.

e.g.
```
export BOSH_CLIENT=ops_manager BOSH_CLIENT_SECRET=fakesecret BOSH_CA_CERT=/var/tempest/workspaces/default/root_ca_certificate  BOSH_ENVIRONMENT=10.0.0.10
```
3. Copy or clone this repository onto this BOSH CLI workstation and create+upload the BOSH release to the director

```
git clone https://github.com/svrc/bosh-symlink-release && cd bosh-symlink-release
bosh create-release --force
bosh upload-release ./dev_releases/bosh-symlink/bosh-symlink-0+dev.1.yml

```
4. Configure the addon from this repo
```
bosh -n update-config --name=bosh-symlink --type=runtime ./addon.yml
```
5. Update your BOSH VMs with a redeploy, e.g. bosh deploy CLI ; TAS for VMs via Ops Manager ; TKGI clusters via the TKGI CLI "upgrade/update-cluster" and/or Ops Manager "Apply Pending Changes" button with the TKGI upgrade errand enabled.  This addon will automatically be installed on all nodes with the default manifest `addon.yml`

