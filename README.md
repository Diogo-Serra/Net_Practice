*This project has been created as part of the 42 curriculum by diosoare.*
 
# NetPractice
 
## Description
 
NetPractice is an introductory networking project. Its goal is to build a working understanding of how small-scale IPv4 networks are addressed, segmented and interconnected, by repairing simulated networks that have been configured incorrectly.
 
The networks are not real. They are presented through a training interface that runs in a web browser and served by a local web server. For each of the 10 levels, the interface displays a non-functioning network diagram together with one or more objectives. The task is to modify the editable fields (IP addresses, subnet masks, default gateways and routing table entries) until every objective is met. Feedback is provided by a **Check again** button and by a log panel that reports faults such as an invalid address or a missing gateway.
 
The project covers the following skills:
 
- Reading and writing IPv4 addresses and subnet masks, in dotted decimal and CIDR notation.
- Calculating the network address, broadcast address and usable host range of a subnet.
- Assigning default gateways and routing table entries so that traffic can leave a subnet and return.
- Distinguishing the roles of hosts, switches and routers.
## Instructions
 
### Running the training interface
 
1. Download the archive attached to the project page and extract it into any folder.
2. From that folder, run the launcher script:
```sh
   ./run.sh
```
 
   The script starts a local web server and opens the default web browser on the training page.
3. If `run.sh` does not work, start the server manually and open the page yourself:
 
```sh
   python3 -m http.server 49242
```
 
   Then navigate to `http://localhost:49242`. The port number may be changed.
 
A local web server is required because of the security restrictions that web browsers apply to locally opened pages.
 
### Using the interface
 
- Enter your login in the field provided so that the interface generates your personal configuration. It is essential to do this before exporting any file.
- Alternatively, the **evaluation** tab generates a random configuration, which is suitable for practice and for evaluation conditions.
- Edit only the unshaded fields of the diagram.
- Select **Check again** to validate the configuration. The log panel at the bottom of the page explains the faults that remain.
- When a level is solved, a button appears that opens the next level.
### Exporting configurations
 
Before moving on from a level, select **Get my config** to download the configuration for that level. One file must be exported per level, and each file must be added to the Git repository.
 
### Submission requirements
 
- The 10 exported configuration files (one per level) must be placed at the root of the repository.
- File names must be checked carefully before submission.
- This `README.md` must also be present at the root of the repository.
- Only the content of the repository is evaluated.
- During the defense, three random levels must be completed within a limited time. No external tools are permitted; a simple calculator such as `bc` is tolerated.
## Resources
 
### Networking concepts studied
 
- **TCP/IP addressing**: the structure of IPv4 addresses, the network and host portions, private and special-purpose ranges, network and broadcast addresses.
- **Subnet masks and CIDR notation**: relationship between masks and prefix lengths, block sizes, and the number of usable hosts per subnet.
- **Default gateways**: the router interface through which a host reaches destinations outside its own subnet.
- **Routers and routing**: interfaces in distinct subnets, routing tables, next hops, the default route (`0.0.0.0/0`), longest prefix match and return paths.
- **Switches**: layer 2 devices that join hosts into a single subnet.
- **OSI model**: the seven layers and their relation to the TCP/IP model, with addressing and routing at layer 3 and switching at layer 2.
### References
 
- RFC 791, *Internet Protocol*: https://www.rfc-editor.org/rfc/rfc791
- RFC 950, *Internet Standard Subnetting Procedure*: https://www.rfc-editor.org/rfc/rfc950
- RFC 1918, *Address Allocation for Private Internets*: https://www.rfc-editor.org/rfc/rfc1918
- RFC 4632, *Classless Inter-domain Routing (CIDR)*: https://www.rfc-editor.org/rfc/rfc4632
- ISO/IEC 7498-1, *Open Systems Interconnection: Basic Reference Model*.
- Cloudflare Learning Center, *What is the OSI model?*: https://www.cloudflare.com/learning/ddos/glossary/open-systems-interconnection-model-osi/

### Use of AI
 
AI was used as a study aid, not to produce the submitted configurations. It was used to:
 
- Produce a structured summary of the concepts listed above (addressing, masks, gateways, routing and the OSI model), which was then read and checked against the references.
- Draft this `README.md`.
All subnet calculations were verified by hand, and the configuration files were produced by solving each level directly in the training interface.
