# Fault-Tolerant-MPI
MPI Library using Replication for Fault Tolerance

## Some Critical files:
src/mpiProfiler.c: Contains all MPI function initializations and implementations, replica remapping implementation, and synchronization before checkpoint/restart.

src/full_context.c: Contains I/O code for checkpoint/restart. Be careful when modifying, as it involves very specific memory-mapping manipulation.

src/client_thread.c: Contains the operations executed by the thread launched by FTHP-MPI. It primarily listens to the coordinator socket and performs operations based on commands received there, such as cleaning up logs and shrinking the communicator.

src/request_handler.c: Contains the code involving looping across all pending MPI requests and testing them for completion. Also contains code that drains in-flight messages by probing the communicators.

src/wrappers.c: Contains the code for intercepting various C functions for correctness, as well as the signal handler code for SIGUSR1.

src/heap_allocator.c: Contains the code for the simple heap allocator implementation that manages the application-space heap.

src/mmanager.c: Contains the code for parsing through /proc/<pid>/maps and obtaining the list of all memory mappings.

src/ibvctx.c: Contains the code for intercepting Infiniband functions. Mostly required for intercepting ibv_poll_cq and preventing errors being passed to the user.

src/commbuf_cache.c: Contains the code for maintaining the communicator buffers that are repeatedly passed to the external MPI library on a recently used basis.

src/tools/parep_mpi_ckpt_coordinator.c: Contains code for the coordinator server that runs on each node.

src/tools/parep_mpi_failure_injector.c: Contains code for the failure injector process.

src/tools/parep_mpi_daemon.c: Contains code for a daemon launched instead of the actual process launcher from the external MPI library. It then launches the actual process launcher and the checkpoint coordinator.

src/tools/mpirun.c: Our main mpirun executable.

src/tools/srun.c: An interception for srun that causes parep_mpi_daemon to be launched on a separate node instead of the actual process launcher

src/preload/ssh.c: An interception for SSH to correctly pass environment variables to the remote nodes.

src/preload/hydra_pmi_proxy.c: This is a temporary solution to intercept the process launcher and launch hydra_pmi_proxy when running on PBS. Ideally, the interception should happen from ssh.c using the same logic as srun.c. This is just a quick fix and should be removed eventually.

src/preload/proxy_hack.c & src/preload/empi_hack.c: Intercepts to functions like waitpid, and poll in external MPI's mpirun and hydra_pmi_proxy (or equivalent).

src/preload/proxy_loader.c: This is used to preload the hacks into the hydra_pmi_proxy (or equivalent).

## Import Environment variables to be set
PAREP_MPI_PATH: Set this to the directory holding the src and bin directories.

PAREP_MPI_BASE_WORKDIR: Set this to a directory holding a folder named checkpoint. This is where your checkpoints would be stored.

PAREP_MPI_EMPI_PATH: Set this to the location of your external MPI library.

PAREP_MPI_LIBUNWIND_PATH: Set this to the location of the libunwind library.

PAREP_MPI_LOGS_SIM_PATH: Set this to the location of the failure simulation logs if you are using them.
