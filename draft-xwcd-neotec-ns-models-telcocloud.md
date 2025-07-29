---
title: "Applicability of IETF-Defined Service and Network Data Models for Telcocloud Network Management"
abbrev: "Telcocloud Network Management"
category: info

docname: draft-xwcd-neotec-ns-models-telcocloud-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "onions"
keyword:

 - Automation
 - Telcom Cloud
 - Network Operation
 - Network Topology

author:
 -
    fullname: Chongfeng Xie
    organization: China Telecom
    email: xiechf@chinatelecom.cn
 -
    fullname: Bo Wu
    organization: Huawei
    email: lana.wubo@huawei.com
 -
    fullname: Nabeel Cocker
    organization: Red Hat
    email: ncocker@redhat.com
 -
    fullname: Linda Dunbar
    organization: Futurewei
    email: ldunbar@futurewei.com

contributor:
 -
   fullname: Jie Dong
   organization: Huawei
   email: jie.dong@huawei.com

normative:

informative:
  ETSI-GS-NFV-IFA-032:
    title: "Interface and Information Model Specification
for Multi-Site Connectivity Services"
    target: https://www.etsi.org/deliver/etsi_gs/nfv-ifa/001_099/032/04.02.01_60/gs_nfv-ifa032v040201p.pdf
    date: May 2021

  ETSI-GR-NFV-SOL-017:
    title: "Report on protocol and data model solutions for
Multi-site Connectivity Services"
    target: https://www.etsi.org/deliver/etsi_gr/NFV-SOL/001_099/017/03.03.01_60/gr_NFV-SOL017v030301p.pdf
    date: May 2021

--- abstract

This document describes how the various data models that are
produced in the IETF can be combined in the context of Telco  Cloud service delivery.

Specifically, this document describes the communication of a Network Orchestrator and Cloud orchestrator for the realization of
optimized Telco Cloud services to implement inter-dc reachability and
connectivity services.

--- middle

# Introduction

The IETF has produced several YANG data models that are instrumental
for automating the provisioning and delivery of connectivity
services.  An overview of these data models and a framework that
describes how these various modules can be used together are
described in {{?RFC8969}}.

This document adopts the rationale of {{?RFC8969}}, but with a focus on
the network coordination with Telco  Cloud services.

The document also identifies some gaps related to existing models.

The document outlines an architecture and communication process
of Network Orchestrators and Cloud orchestrators.


# Conventions and Definitions

This document assumes that the reader is familiar with the contents
of {{?RFC6241}}, {{?RFC7950}}, and {{?RFC8309}} as it uses terms from those RFCs.

This document uses the term "network model" as defined in Section 2.1
of {{?RFC8969}}.

# A Reference Architecture of Telco Cloud Network Coordination

In some implementation, Cloud Orchestrator and Network Orchestrator
are only associated with their own respective services. That is, Cloud Orchestrator is
responsible for data center (DC) services, such as application,
compute, and/or storage services within the DC, while network
 Orchestrator plans and deploys connections based on planed inter-DC
traffic demands.

In certain scenarios, such as the {{ETSI-GS-NFV-IFA-032}} cross-site connectivity service interfaces definition,
to support cloud-based cross-data center (DC) scaling, the NFV cloud platform can leverage network-exposed API interfaces to dynamically collect underlay
WAN network status and establish/update inter-DC connections. The architecture option is shown in the figure below.

~~~~ aasvg
  +----------------+                     +--------------------+
  | Cloud          |                     |Network Orchestrator|
  | Orchestrator   |<------------------->|                    |
  |( Telco Cloud   | IETF Service&Network|(Provider Network   |
  | Orchestration) |   Data models/API   |  Orchestration)    |
  +----------------+                     +--------------------+
                 |                          |
                 |                          |
                 |                          |
 +---------------|-+                      +-|---------------+
 | +--+          v |                      | v               |
 | |NF+   +----+ .1|    192.0.2.0/31      |.0+--+           |
 | +--+...+DCGW+-----------------------  ----+PE|           |
 |+---+   +----+   |      VLAN 100        |  +--+           |
 ||APP|            |                      |                 |
 |+---+            |                      |     Provider    |
 |  DC Network     |                      |     Network     |
 +-----------------+                      +-----------------+
                |----------- AC -------  ----|


Legend: DCGW: DC Gateway
~~~~
{: title="Coordination of Network Resources for the Cloud Service Provisioning" artwork-align="center"}

This NFV cloud architecture option implies that the Cloud Orchestrator operates with network-awareness.
The Network Orchestrator exposes the interfaces to provide pre-planned bandwidth and dynamic connections. The Cloud Orchestrator can then dynamically control and manage the capacity and network connections between WANs of inter-DC.
As suggested in {{ETSI-GR-NFV-SOL-017}}, The candidate IETF interfaces between Network Orchestrator and Cloud Orchestrator are outlined in the table below:

| Number | Network Function Requirements     | IETF YANG Models                                                                 |
|--------|-----------------------------------|----------------------------------------------------------------------------------|
| 1      | Multi-site Connectivity           | - L3SM [RFC8299]<br>- L3NM [RFC9182]<br>- L2SM [RFC8466]<br>- L2NM [RFC9291]<br>- RFC9543 NSS YANG Model<br>- [I-D.ietf-teas-ietf-network-slice-nbi](https://datatracker.ietf.org/doc/I-D.ietf-teas-ietf-network-slice-nbi/)<br>- AC Service YANG Model [I-D.ietf-opsawg-teas-attachment-circuit](https://datatracker.ietf.org/doc/I-D.ietf-opsawg-teas-attachment-circuit/) |
| 2      | Capacity Management               | - Service Attachment Point [RFC9408]<br>- TE Service Mapping [I-D.ietf-teas-te-service-mapping-yang]<br>- TE Topology [RFC8975]<br>- TE Tunnel [I-D.ietf-teas-yang-te]<br>- SR Policy [I-D.ietf-spring-sr-policy-yang] |
| 3      | Fault Management                  | - Alarm Management [RFC8632]                                                  |
| 4      | Performance Management            | - Network and VPN Service PM [RFC9375]                                        |


However, this NFVO architecture option cannot meet the emerging needs of telecom cloud applications because it is difficult to plan bandwidth between large-scale edge DCs and enterprise sites.
For example, AI-based video analysis and AI-driven knowledge reasoning will be deployed in edge data centers,
which are characterized by a large number of deployments and significant variations in resource capabilities.
Combined with the diversity of enterprise sites, the new telecom cloud needs fast, private, and reliable connections between site-to-site, site-to-cloud, or cloud-to-cloud endpoints.

A potential alternative architecture option involves centralized scheduling of cloud
and network resources to coordinate their integration, enabling rapid
deployment of network services and applications such as SD-WAN, SASE,
and other edge cloud services. This approach ensures agile allocation of cloud resources and
optimization of WAN network resources to meet dynamic demand.

~~~~ aasvg


            +----------------------------+
            | Customer Service Requester |
            +----------------------------+
                           |
                   Service | (e.g.Edge Cloud services,
                      API  |        SD-WANs,SASE)
                           |
                           |
       +-----------------------------------------+
       |    Telco Super Service Orchestrator     |
       +-------+----------------------+----------+
               |                      |
               |Network API           |Cloud API
  +------------+----------+           |
  |  Network Orchestrator |     +-----+-------+
  +------------+----------+     |    Cloud    |
      Network  |                | Orchestrator|
      YANG     |                |             |
      Model    |                +-----+-------+
  +------------+----------+           |
  |  Network Controller   |           |
  | (Overlay & Underlay)  |           |
  +-----------------------+           |
               |                      |
       Device  |               +------+------+
       Models  |               |    DC 1     |
               |               |    +-------------+
               |               +----|    DC-N     |
--------------------------          |             |
         WAN  Network               +-------------+




~~~~
{: title="Alternative Architecture of the Cloud Service Provisioning" artwork-align="center"}

This proposed telco cloud reference architecture is an open framework to allow for multiple
vendor Network Orchestrators and multiple vendor Cloud
Orchestrators. The goal is to enable standard data models or APIs to provide those services.

# Telco Cloud Service Requirements

## Optimized Scenarios of Telco Cloud Service Placement

### Example of Overlay Network and Cloud Service Placement


The diagram below illustrates a telco cloud network example connecting multiple data centers (DCs) and
enterprise CEs. Multiple data centers are shown, each potentially
hosting different services or applications. For instance, DC-1 is
directly linked to gateway GW2A and GW2B, suggesting it may host
critical applications or services that require high availability.
Various DC gateways are deployed to manage traffic flow towards
Data Centers or Application instances.
Two CE1 devices are connected to PE5A and PE5B respectively,
indicating the start points for customer traffic entering the provider's network.
There are several Provider Edge devices (PE5A, PE5B, etc.) which serve
as entry and exit points for traffic moving between the customer and the provider's network.
The Border routers (BRs) facilitates the transfer of data across different parts of the network.
They connect the access/aggregation layer to the core network.

~~~~ aasvg

             +-----------------+     +-----------------+
             |                 |     |                 |
+---+       +----+             |     |                 |
|CE1+-------|PE5A|             |     |                 |
+---+       +----+             |     |                 |
             |                 |     |                 |
             |                 |     |                 |  +------DC 1 ---+
             |                 |     |                 |  |              |
+---+       +----+         +----+   +----+         +---++ |+----+        |
|CE2+-------|PE5B|         |BR2A|---|BR1A|         |PE1A|-+|GW2A|        |
+---+       +----+         +----+   +----+         +----+ |+----+        |
             |    Access/Agg   |     |     Core        |  |      +------+|
             |                 |     |                 |  |      |APP A ||
+--------+   |     Network     |     |     Network     |  |      +------+|
|        |  +----+         +----+   +----+         +----+ |+----+        |
| DC-4   |--|PE4A|         |BR2B|---|BR1B|         |PE1B|-+|GW2B|        |
|        |  +----+         +----+   +----+         +----+ |+----+        |
+--------+   |                 |     |                 |  +--------------+
             |                 |     |                 |
+--------+   |                 |     |                 |
|        |  +----+             |     |                 |
| DC-5   |--|PE4B|             |     |                 |
|        |  +----+             |     |                 |
+--------+   |   +----+ +----+ |     | +----+  +----+  |
             +---|PE3A|-|PE3B|-+     +-|PE2A|--|PE2B|--+
                 +-+--+ +-+--+         +-+--+  +--+-+
                   |      |              |        |
            +------+------+------+  +----+--------+------+
            |    +-+--+ +-+--+   |  |  +-+--+  +--+-+    |
            |    |GW1A| |GW1B|   |     |GW3A|  |GW3B|    |
            |    +----+ +----+   |  |  +----+  +----+    |
            |                    |  |                    |
            |                    |  |                    |
            |                    |  |                    |
            |  +------+ +------+ |  |                    |
            |  |vCPE  | |APP A | |  |                    |
            |  +------+ +------+ |  |                    |
            |                    |  |                    |
            +---------DC-3-------+  +-------DC-2---------+
Legend: GW: DC Gateway

~~~~
{: title="Coordination of Network Resources for the Cloud Service Provisioning" artwork-align="center"}

To create services across DCs like optimized service placement,
generic API calls are needed. A typical usage is to use Restful APIs
of Cloud Orchestrator and Network Orchestrator and used by
the Super Orchestrator to provision the inter-DC services connections and also applications.


When deploying cross-DC cloud services, it is assumed that the Super
 Orchestrator has access to the DC and network connectivity topology (e.g. TE
Topology {{?RFC8975}}), as well as centralized resource information for both the DCs and the network.
Some standard network inventory interfaces are available. For example,
the Service Attachment Points (SAPs) {{?RFC9408}} or ACs {{?I-D.ietf-opsawg-teas-attachment-circuit}} can obtain the AC/Bearer
information of the PE, which suggests that the private line service
provisioning resource resources on the network side, but there is no
cloud DC service provisioning resource information, such as CPU, GPU,
storage, and DC network information. DC aware TE topology model {{?I-D.llc-teas-dc-aware-topo-model-04}} defines YANG models about the information. One API example is as below.

~~~~ aasvg
GET /api/cloud/resources?type=compute&network&location=dc-3
{
  "compute": {
    "vCPU": 100,
    "GPU": 2,    
    "memory": "1280GB",
    "storage": "10TB",
    "instance_type": "large"
  },
  "network": {
    "availability": {
      "throughput": "10 Gbps",
      "latency": "<1ms",
      "packet_loss": "0.001%",
      "supported_protocols": ["VxLAN", "GRE"]
    },
    "subnets": [
      {
        "cidr": "10.1.0.0/24",
      }
    ],
  }
}
~~~~
{: title="Cloud Inventory API" artwork-align="center"}


The flowchart below gives an example of hospital applications are deployed
and ensure efficient use of network connections for optimized data flow.

~~~~ aasvg

+-----------------------------+
|1 Super Orchestrator         |
|                             |
|   Requirement Analysis &    |
| Resource Allocation Decision|
|                             |
| Branch Data Transfer:       |
|Bandwidth 100Mbps,Latency<50ms
|Secure, AI Analysis          |
|                             |
|Select Edge DC and compute   |
|dedicated Line               |
+-----------------------------+
               |
               |
               |
               v
+-----------------------------+
|2   Cloud Orchestrator &     |
|    Network Orchestrator     |
|                             |
|   Application & Network     |
|   connections deployment    |
|   Center DC:AI training     |
|   Edge DC:AI inference      |
+-----------------------------+
                |
                |
                v
+-----------------------------+
|3 Super Orchestrator         |
|                             |
|   Validation and Monitoring |
|(Test Transmission Latency:  |
|48ms Check Data Integrity)   |
+-----------------------------+



~~~~
{: title="Cloud Service Placement" artwork-align="center"}

Here is a step-by-step breakdown of the flowchart with detailed explanations for each step:

1. Step 1: Requirement Analysis: This is the first step where the Super Orchestrator gathers and analyzes requirements for branch data transfer.
Key Requirements are bandwidth must be at least 100 Mbps, data must be transmitted over a private connectionbe, and support AI analysis.
the Super Orchestrator also determines the optimal allocation of resources for data transfer. Data will flow from the branch office to the center data center (DC).
A edge DC is selected as an intermediate hop. A dedicated line of 100 Mbps of bandwidth with redundant links and to the center and edge DC is needed.
Underlay Network Controllers may expose to Network Orchestrator s a set of network data models, such as the AC, L3SM {{?RFC8299}}, L3NM {{?RFC9182}}, L2SM {{?RFC8466}}, L2NM {{?RFC9291}}, RFC9543 NSS YANG Model {{!I-D.ietf-teas-ietf-network-slice-nbi-yang}},
Service Attachment Points (SAPs) {{?RFC9408}}, TE Service Mapping {{?I-D.ietf-teas-te-service-mapping-yang}}, TE Tunnel {{?I-D.ietf-teas-yang-te}}, or SR Policy {{?I-D.ietf-spring-sr-policy-yang}}.
Network Orchestrator can use these models to set up connections between the Provider Edge devices, and also customer facing ACs between CEs and PEs, DC-GWs and PEs.

2. Step 2: Application Instances Deployment and Parallel Network Connectivity: This step involves deploying AI at the center DC and the edge DC. 
With request from Super Orchestrator, Cloud Orchestrator allocates resources, including GPU clusters and storage.
Also Network Orchestrator can configure PE.

5. Step 3: Validation and Monitoring: This step ensures that the service meets performance and reliability expectations.
Through the open interfaces, Super orchestrator can monitor status of cloud resources and WAN connections.
The open interfaces could be VPN PM {{?RFC9375}} ,Alarm Management {{?RFC8632}}, or new service assurance models.

The steps described above assume that ​​Super Orchestrator​​ can access
both network and cloud resources to perform resource analysis and
allocation.


#### Example of Telco Cloud Service Creation Flow

An example of service creation flow is as follows:

~~~~
 +------------------+         +---------------+    +----------------+
 |Super Orchestrator|         | Cloud Orchestr|    |Network Orchestr|
 +------------------+         +---------------+    +----------------+
      |                               |                        |
      |                               |                        |
      |1.1 Create a cloud service     |                        |
      |------------------------------>|                        |
      |                               |                        |
      |                   +--------------------------+         |
      |                   |2.Deploy the cloud service|         |
      |                   +--------------------------+         |
      |                               |                        |
      |         1.2 Create a network service                   |
      |------------------------------------------------------->|
      |                               |                        |
      |                               |   +----------------------------+
      |                               |   |2.Deploy the network service|
      |                               | +------------------------------+
      | 3.Create cloud service response                        |
      |-------------------------------|                        |
      |                               |                        |
      |                               |                        |
      |         3. Create Network service response             |
      |--------------------------------------------------------|
      |                               |                        |
      |                               |                        |
      |                               |                        |
      | 4. Subscribe for cloud performance metric              |
      |------------------------------>|                        |
      |                               |                        |
      |             5.Subscribe for network performance metric |
      |------------------------------------------------------->|
      |                               |                        |
 +------------------------+           |                        |
 |                        |           |                        |
 |5.Continuous monitoring |           |                        |
 +------------------------+           |                        |
      |                               |                        |
      |                               |                        |

~~~~
{: title="Cloud Service Creation" artwork-align="center"}


### More Examples to Add

## Optimized Scenarios of Telco Cloud Service Assurance


A scaling example is to add.



# Security Considerations
TBC.

# IANA Considerations

None.

--- back

# Acknowledgments
{:numbered="false"}

The authors wish to thank xxx and many others for their helpful comments and
suggestions.
