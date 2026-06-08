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
