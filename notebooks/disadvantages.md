# Disadvantages in this design

## Complexity within infrastructure

Within this design, we have moved a lot of the complexity out from code into infrastructure. Although each piece of the infrastructure is pretty small and isolated, understanding the larger system is very complex. It can also be difficult to track the consequences of changes. Even though we try to limit the number of assumptions in the design stage for each piece of the system, they do by necessity have some assumptions they make, because no one tool has visibility into the entire system. It is simple for a new developer to accidentally violate those assumptions. 

## Limited information is available to each service

Since we are passing around small events, rather than running within some larger system, it is difficult for individual services to access information. We have needed to carefully design shared database tables and other locations for services to retrieve system-wide information from, while also ensuring that there are limited spots where that information is updated, so the information is consistent within a particular run of the whole program. 

## Difficult to get visibility into the entire system

Similarly, it is hard for humans using and working on the SDC to get visibility into the overall health of the system. We have monitoring and tracking set up at various places in the system, and we use these tools to update an expected processing database. However, it's not easy to assemble information from all the different services into one convenient place.


