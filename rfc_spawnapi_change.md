# RFCnnnn
The RFC number will be provided upon submission.

## Title
Modify the Spawn API to Restart Failed or Spawn New Processes in The Same Namespace.

## Abstract
The PMIx_Spawn API as it is defined and implemented today only allows for spawning
new processes in a different namespace mimicking the behavior of MPI Spawn. This RFC
is to extend the spawn interface to (re)spawn failed process and start new processes
in the same namespace. The restart of failed process is required to support application
directed error recovery actions for a certain class of errors like node/network
connection failures. Spawning of new processes in the same namespace is required to
support evolving/malleable/adaptible applications (flexible allocation support requirement)

## Labels
* [MODIFICATION] - modifies an existing PMIx definition (API, key string, etc.)
* [BEHAVIOR] - modifies the underlying behavior of an existing PMIx function

## Action

## Copyright Notice
This document is subject to all provisions relating to code contributions to the PMIx community as defined in the community's [LICENSE](https://github.com/pmix/RFCs/tree/master/LICENSE) file. Code Components extracted from this document must include the License text as described in that file.

## Description
The PMIx_Spawn API by default assumes the behavior of MPI spawn and spawns the
requested processes in a new namespace and connects the calling process (parent)
with the newly spawned namespace. With PMIx support for enabling application directed
error response and flexible allocation, the application needs to be able to spawn
replacement processes (restart of failed processes) for error recovery and spawn
new processes for evolving jobs (adaptive and malleable) in the same namespace.
In order to be able to do this the RFC proposing following modifications to the
spawn API:
(1) Add new parameter PMIx_Spawn and PMIx_Spawn_nb directive and modify pmix_spawn_cbfunc_t to return
the info keys from the original call

 pmix_status_t PMIx_Spawn(uint32 pmix_spawn_directive,
                          const pmix_info_t job_info[], size_t ninfo,
                          const pmix_info_t alloc_info[], size_t ninfo,
                          const pmix_app_t apps[], size_t napps,
                          char nspace[]);
 @ param pmix_spawn_directive values
 #define PMIX_SPAWN_IN_NEW_NAMESPACE 0
 #define PMIX_SPAWN_IN_SAME_NAMESPACE 1
 #define PMIX_SPAWN_RESTART_IN_NAMESPACE 2

 @param alloc_info - contains info keys specific to allocation reserved for the spawn
#define PMIX_ALLOC_ID              "pmix.allocid"           // uint32 - allocation id provided by the RM
#define PMIX_NET_TOPO              "pmix.ntopo"             // (char*) xml-representation of network topology
#define PMIX_LOCAL_TOPO            "pmix.ltopo"             // (char*) xml-representation of local node topology
#define PMIX_NODE_LIST             "pmix.nlist"             // (char*) comma-delimited list of nodes running procs for this job
#define PMIX_TOPOLOGY              "pmix.topo"              // (hwloc_topology_t) pointer to the PMIx client's internal topology object
 pmix_status_t PMIx_Spawn_nb(uint32 pmix_spawn_directive,
                          const pmix_info_t job_info[], size_t ninfo,
                          const pmix_info_t alloc_info[], size_t ninfo,
                          const pmix_app_t apps[], size_t napps,
                          char nspace[], omix_spawn_cbfunc_t cbfunc,
                          void *data);

typedef void (*pmix_spawn_cbfunc_t)(pmix_status_t status,
                                    char nspace[],
                                    const pmix_info_t job_info[], size_t ninfo,
                                    const pmix_info_t alloc_info[], size_t ninfo,
                                    void *cbdata);
The spawn callback will provide the original allocation info keys, the job info keys can be

## Protoype Implementation
Provide a reference link to the accompanying Pull Request (PR) against the PMIx master repository. If the prototype implementation has been tested against an appropriately modified resource manager and/or client program, then references to those prototypes should be provided. Note that approval of any RFC will be far more likely to happen if such validation has been performed!

## Author(s)
Annapurna Dasari
Intel Inc.
annu13@github
