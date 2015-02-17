Active Directory
==========
Chris Sivanich ©2015

----------

- DC domain controller
    - Multiple may be added
        - Clustering
- Clients connect to DC and become part of domain
- Sites
    - Combine DCs across locations
    - Site HQ has 10 DCs
        - Fast replication
    - Satelite DCs
        - RO to ensure security upstream
        - Slow replication
        - Replication strategies move data
            - Though site link
            - Can use bridge heads for satelities to connect
                - Provides backup replication route
- Global indexes
    - Locations for given data
        - Fast search for users
    - Global catalog
 - DNS
