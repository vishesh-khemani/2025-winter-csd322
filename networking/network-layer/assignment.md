# Assignment: Network Layer

1. Name two fields in the IPv4 header of a datagram that are always changed by every router that processes the datagram.

    <!-- TTL and Checksum (because TTL changes) -->

2. If an application message of 100 bytes is sent over the network using UDP and IPv6, what percentage of the total datagram size is the application message (rounded to the nearest integer percent)?

    <!-- 100 / (100 + 8 + 40) * 100 % + 68% (approx.) -->

1. Consider a datagram network using CIDR addressing. Suppose a router has the following forwarding table:

    | Network prefix | Interface |
    | ------------ | --------- |
    | 123.1.1.128/25 | 0         |
    | 123.1.1.128/26           | 1         |
    | 123.1.1.192/27          | 2         |
    | otherwise    | 3         |

    For each of the interfaces 0, 1, 2, give the associated range of destination host addresses and the number of addresses in the range.

    <!--
    Interface 2: 123.1.1.192 to 123.1.1.223, 2^5 adresses
    Interface 1: 123.1.1.128 to 123.1.1.191, 2^6 addresses
    Interface 0: 123.1.1.224 to 123.1.1.255, 2^5 addresses
    -->

1. Suppose an ISP owns the block of addresses of the form 128.119.40.64/26. Suppose it wants to create four subnets from this block, with each block having the same number of IP addresses. What are the prefixes (of form a.b.c.d/x) for the four subnets?

    <!--
    Each subnet will be a /28 with the following last byte in the subnet mask:
    64 + (0*32 + 0*16) = 64 => 128.119.40.64/28
    64 + (0*32 + 1*16) = 80 => 128.119.40.80/28
    64 + (1*32 + 0*16) = 96 => 128.119.40.96/28
    64 + (1*32 + 1*16) = 112 => 128.119.40.112/28
    -->

1. Consider the following network. With the indicated link costs, uses Djikstra's shortest-path algorithm to compute the shortest path from $x$ to $u$, $w$, and $y$. <!-- NETWO-R6lfp -->

    ![alt text](<Screenshot 2025-03-09 at 5.37.17 PM.png>)

    <!-- 6, 6, 6 after step 2 -->

1. Consider the network shown below, and assume that each node initially knows the costs to each of its neighbors. Consider the distance-vector algorithm. <!-- NETWO-IllSd -->

    ![alt text](<Screenshot 2025-03-09 at 2.56.42 PM.png>)

   1. What is the initial distance vector at $z$?

        <!--
        Initial $\vec{D_z} = [\infty, 6, 2, \infty, 0]$
        -->

   2. What is the updated distance vector at $z$ after it recives the initial distance vector from $x$?  

        <!--
        Initial $\vec{D_x} = [\infty, 3, 0, 3, 2]$
        c(z, x) = 2
        After receiving $\vec{D_x}$, $\vec{D_z} = [\infty, min(6, 2+3)=5, 2, min(\infty, 2+3)=5, 0]$
        -->

   3. What is the updated distance vector at $z$ after it recives the initial distance vector from $v$?

        <!--
        Initial $\vec{D_v} = [1, 0, 3, \infty, 6]$
        c(z, v) = 6
        After receiving $\vec{D_v}$, $\vec{D_z} = [min(\infty, 6+1)=7, 5, min(2, 6+3)=2, 5, 0]$
        -->
