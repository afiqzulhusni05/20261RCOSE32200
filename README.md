# COSE322 - System Programming Project 2

## Student Information
- Name: Muhammad Afiq Zulhusni bin Zailani
- Student ID: 2023320091
- Department: Computer Science and Engineering

## Environment Specifications
- Guest VM OS: Ubuntu 22.04 LTS
- Kernel Version: 5.15.198

## Phase 1 - Network Address Translation (NAT)

Implements basic static destination and source header translations at the Traffic Control (TC) ingress and egress hooks to establish seamless communication with the Virtual IP cluster.

## Phase 2 - Stateful Load Balancing Maps
- flow_counter_map (BPF_MAP_TYPE_ARRAY) - Core connection index counter
- flow_table_map (BPF_MAP_TYPE_HASH) - Stateful session tracker tracking 5-tuple metrics

## Infrastructure Virtual Environments
Orchestrates isolated networks using isolated network namespaces: ns_client, ns_db_main, and ns_db_backup connected over standard veth tunneling pairs.

Verification complete. Network ledger traces captured via tcpdump successfully map transactions across virtual interfaces.

## Kernel Subsystem Constraints
- Subsystem: Linux Traffic Control (TC) Classifier
- Program Hook Hook Point: SEC("classifier")
- Kernel Return Actions: TC_ACT_OK (pass packet through natively)

## Stateful Flow Session Tracking Signature
The connection key maps the precise 5-tuple signature layer:
1. Client IP Address (Source)
2. Virtual IP Cluster Destination (VIP)
3. Client Dynamic Port Allocation
4. VIP Listening Gateway Port (8080)
5. Layer 4 Protocol Type (IPPROTO_TCP)

## Packet Integrity Management
Header field translation requires live incremental recalculation of Layer 3 and Layer 4 checksum parameters. This prevents the host OS networking engine from invalidating modified packets upon receipt.
- Helpers used: bpf_l3_csum_replace, bpf_l4_csum_replace

## Stateful Connection Handshake Workflow
1. Client sends TCP SYN packet -> Hits VIP:8080
2. eBPF rewrites destination -> Lands at Main/Backup Backend port
3. Backend returns TCP SYN-ACK -> Handled by egress hook
4. eBPF masks source back to VIP -> Client receives clean verification handshake

Infrastructure Clean Teardown Rules:
- De-register active TC filter pipelines from veth hooks
- Purge kernel array and hash maps to free kernel allocation tables
- Execute teardown_network.sh to delete virtual namespaces cleanly
