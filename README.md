# networking-recipe

IPDK Networking Recipe (P4 Control Plane)

## Background

The initial implementation of P4-OVS was both physically and logically
monolithic.

- Two foreign components (Stratum and the Kernel Monitor) were added to a
  fork of the Open vSwitch repository.

- OvS was modified to build these components and include them in
  `ovs-vswitchd`.

- Stratum initialization was adapted to allow OvS to start the P4 Runtime
  and gNMI services.

- Extensive changes were made to Stratum to support P4-enabled IPDK hardware
  and software switches.

## Transition to P4 Control Plane

### Completed

The P4 Control Plane (formerly known as the Split Architecture) modularizes
P4-OVS, making the code easier to maintain and more suitable for upstreaming.

- Removed foreign components from the OvS repository, together with most
  of the changes to OvS itself.

- Reengineered the Stratum modifications to be non-breaking and suitable for
  upstreaming to the parent project. Made `tdi` a distinct platform type,
  with `tofino` and `dpdk` variants (targets).

- Extracted the Kernel Monitor and make it a separate component. Removed
  OvS dependencies, and refactored for modularity and to support unit testing.

- Created a new component (`infrap4d`) that combines Stratum, the Kernel
  Monitor, TDI, and a P4 target driver into a separate process (daemon).

- Created a superproject (`networking-recipe`) to integrate the components
  and orchestrate the overall build.

- Updated OvS to a more recent version.

### In Progress

- Integrate the Kernel Monitor into `infrap4d`. Merge changes that were
  made in P4-OVS after the original split was done.

- Create a component (`ovs-p4rt`) that can be linked with `ovs-vswitchd`
  to allow OvS to communicate with `infrap4d`.

- Update to the current version of Stratum.

- Upstream the OvS and Stratum changes to the parent projects.

## Changes from P4-OVS

- The `external`, `p4runtime`, `stratum`, and `unit_test` directories
  have been removed from the `ovs` repository.

- The `p4proto` directory has been removed. Locally-modified Stratum files
  were merged back into Stratum or discarded. Target-specific changes were
  made part of the `dpdk` and `tofino` variants of the `tdi` platform.

- OvS no longer manages Stratum or the Kernel Monitor. It also no longer
  makes direct calls to the Kernel Monitor or switch driver. All communication
  with Stratum will be by means of calls to the OvS client API (`ovs-p4rt`),
  which sends gRPC requests to `infrap4d`.

- The `barefoot` Stratum code has been restored to its original state.

## Repositories

Implementation is being done incrementally through ipdk-io repositories.

- OvS development takes place on the `split-arch` branch of the
  `ipdk-io/ovs` repository.

- Stratum development takes place on the `split-arch` branch of the
  `ipdk-io/stratum-dev` repository.

- Kernel Monitor development takes place on the `main` branch of the
  `ipdk-io/krnlmon` repository.

- Superproject development takes place on the `main` branch of the
  `ipdk-io/networking-recipe` repository.
