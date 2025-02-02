Creation of the UOS_IOTSH_2024 Dataset

Our survey of the existing literature at the time of producing this dataset (~March 2024) determined that there is no readily available comprehensive dataset targeting the detection of a sinkhole attack on an RPL-based IoT network covering different network sizes and attack scenarios.
Existing datasets either did not address the specific characteristics of IoT, did not consider the sinkhole attack specifically, or did not consider a broad enough spectrum of network sizes, network topographies, and attack scenarios. Therefore, we embarked on the creation the UOS-IOTSH_2024 Dataset that should be comprehensive enough to support future research work on detection of sinkhole attacks. The following subsections will describe the development of the UOS_IOTSH_2024 dataset.
Attack Scenario
The UOS_IOTSH_2024 Dataset simulates an internal attack scenario, where an intruder infiltrates a node that is already integrated into a functioning RPL-based IoT network to execute a sinkhole attack. This scenario mimics a particularly dangerous form of attack, where an adversary leverages a compromised node's established network privileges to orchestrate malicious activities. By compromising an existing node within the network, the attacker gains the ability to covertly manipulate the network's communication dynamics, directing traffic flow in a manner that disrupts normal operations and compromises network integrity.
Once the intruder has gained access to the victim node, it will modify the rank information advertised by the compromised node. By falsely advertising an optimal path to the root node, the attacker can attract traffic towards the compromised node, effectively hijacking the network's communication pathways. Because the attack originates from within the network, it can be very challenging to detect using traditional security mechanisms.

Requirements for the UOS_IOTSH_2024 Dataset
The UOS_IOTSH_2024 Dataset has been developed to meet the following requirements:
- Network Topologies. The dataset should encompass a variety of network topologies, including small-scale and medium-scale networks, to represent different deployment scenarios commonly encountered in RPL-based IoT networks.
- DODAGs. The dataset should include networks with varying numbers of DODAGS to investigate the impact of multiple DODAGs on detection accuracy and latency.
- Sinkhole Attack Scenarios. The dataset should represent different sinkhole attack scenarios, including single sinkhole attacks and double sinkhole attacks, with varying degrees of severity and impact on network behavior.
- Attacker Locations. The dataset should include attackers in different locations within the hierarchy to enable assessing the effect of the attacker's location on detection accuracy.
- Anomalous Patterns. The dataset should include instances of normal network operation as well as network operation under sinkhole attack to facilitate the development and evaluation of ML-based detection methods.
- Realistic Network Traffic. The dataset should incorporate realistic network traffic patterns, including both control and data packets, to accurately simulate the behavior of RPL-based IoT networks.
- Non-Invasive Detection. The dataset should be based on the standard control packets generated by the RPL protocol without any modifications to the protocol.
- Ground Truth Labels. The dataset should be annotated with ground truth labels indicating the presence or absence of sinkhole attacks in each scenario, allowing for supervised learning approaches in ML/DL-based detection methods.

These requirements have been used to guide the development of the dataset as will be shown in the coming subsections.

Composition of the UOS_IOTSH_2024 Dataset
Based on the above requirements, the dataset was created with the following main categories:
- Traffic from RPL-based IoT networks operating normally under multiple scenarios:
-- A small network with a single DODAG.
-- A medium-sized network with a single DODAG.
-- A medium-sized network with dual DODAGs.

- Traffic from RPL-based IoT networks under a single sinkhole attack:
-- A small network with a single DODAG (multiple instances for different attacker locations)
-- A medium-sized network with a single DODAG (multiple instances for different attacker locations)
-- A medium-sized network with dual DODAGs (multiple instances for different attacker locations)

- Traffic from RPL-based IoT networks under dual sinkhole attacks:
-- A small network with a single DODAG (multiple instances for different attacker locations)
-- A medium-sized network with a single DODAG (multiple instances for different attacker locations)
-- A medium-sized network with dual DODAGs (multiple instances for different attacker locations)

The Attack Effect Numerical Measure
Under certain circumstances it becomes very difficult and time consuming to exhaustively investigate all possible attack scenarios on a particular setup. This is especially true when multiple attackers may be present in the network and the possible combinations of scenarios becomes prohibitively large. Under these circumstances it pays to concentrate the effort on the worst-case scenarios which would cause the worst damage to the network traffic.

We created a numerical measure to assess the effect of any particular attack on the exchanged traffic in the network. The Sinkhole Attack Effect measure reflects the attack's success in redirecting network traffic toward the compromised node(s). Essentially, it estimates the percentage of total network traffic routed through the compromised node(s) following the attack. A higher percentage indicates a more severe impact of the attack on the network's normal operation. The Sinkhole Attack Effect measure is calculated according to the expression in the following equation:
"Sinkhole Attack Effect=("Number of Affected Nodes")/("Total Number of Sensor Nodes" - "Number of Attacking Nodes")

where “Affected Nodes” refers to nodes whose traffic passes directly or indirectly through the compromised node(s) after the attack.

Determination of Appropriate Network Sizes to Simulate
Analysis of the literature available at the time of creating this dataset (~March 2024) has shown that previous researchers have used three ranges of network sizes as shown below:
Network Size	Number of nodes	References
Small	Less than 20	[1], [2], [3], [4], [5], [6], [7], [8], and [9]
Medium	Less than 50	[1], [5], [10], [11], and [12]
Large	More than 100	[13], [11], [14], [15], [16], [10], [17], [18], and [19]

Accordingly, it was decided to adopt a network size of 12 sensor nodes (in addition to the root node) to represent a small network and a network size of 24 sensor nodes (in addition to one or two root nodes) to represent the medium-sized network.


Features Included in the UOS_IOTSH_2024 Dataset
The COOJA simulator allows access to a large number of features and parameters from the RPL packets. However, not all of these features are used during the crucial period of constructing the DODAG. Additionally, most of these features are not affected by the nature of the sinkhole attack. Given that the UOS_IOTSH_2024 dataset is targeted specifically at sinkhole attack scenarios, we chose to include only a small collection of fields in the dataset to keep the number of features manageable and concentrate on the fields that may be affected by the sinkhole attack. We considered the following fields for inclusion:
- All the fields in the RPL control messages that are used for constructing and maintaining the DODAG and controlling the operation of the network.
- The DODAG configuration options which are configured in the root node and distributed to the rest of the DODAG nodes.
- Certain details obtained from the IPv6 packet header, such as the source and destination IP addresses.

Then, these parameters were filtered by assessing the impact of the attack on each specific feature. Accordingly, the dataset was first created with the 6 features shown below extracted directly from the raw network traffic collected from the COOJA simulations.
-----------------------------------------------------------------------------------------------------------------------------------------
| Field			| Feature Description			| RPL Control Message	| Reason of inclusion or omission		|
-----------------------------------------------------------------------------------------------------------------------------------------
| Source ID		| IPv6 address of the source node	| IPv6 header		| Used to identify child and parent nodes, 	|
| Destination ID	| IPv6 address of the destination node	| 			| which are affected by the sinkhole attack	|		
-----------------------------------------------------------------------------------------------------------------------------------------
| Rank			| Relative position of node relative to | DIO			| This is the exact attack target of sinkhole 	|
|			| other nodes with respect to a DODAG 	|			| attack.					|
|			| root.					|			|						|
-----------------------------------------------------------------------------------------------------------------------------------------
| Length		| The length of the RPL control message | DODAG Configuration 	| The sequence of the control messages between 	|
|			| (64: DIS, 97 or 102: DIO, 76: DAO or 	| Option		| the nodes is affected by the sinkhole attack.	|
|			| 5: DAO ACK).				|			|						|
-----------------------------------------------------------------------------------------------------------------------------------------
| Info			| Indicate the type of the RPL	 	| DODAG Configuration 	| To indicate the type of the control message. 	|
|			| control message			| Option		| The attack utilized DIO message to announce 	|
|			|					|			| the false rank, and the DAO message is	| 
|			|					|			| affectedby the attack existence due to the	|
|			|					|			| re-electionof the parent.			|
-----------------------------------------------------------------------------------------------------------------------------------------
| Protocol		| The communication protocol 		| DODAG Configuration 	| To simulate the real network traffic 		|
|			| (ICMPV6, IEEE802.1 or UDP).		| Option		|						|
-----------------------------------------------------------------------------------------------------------------------------------------

Procedure for Simulating the Attack using the COOJA Simulator
The procedure for simulating the internal sinkhole attack described in the attack scenario of section ‎1 proceeded according to the following steps:
- The chosen attack scenario assumes an internal attack leveraging a node that is already integrated into an existing DODAG. Therefore, after the network is constructed in COOJA, it will be allowed to run normally until the DODAG is constructed successfully and the network starts to transmit UDP data packets. Through preliminary experimentation, we determined that this process typically requires approximately 100 seconds of simulation time. Accordingly, the network will be allowed to run normally for 100 seconds to achieve convergence and stability, then the attack will be launched.
- After the conclusion of the 100 seconds, a designated node will alter its behavior and launch the sinkhole attack by broadcasting DIO messages that falsely advertise a rank very close to that of the root node. The location of this compromised node will be varied to investigate the impact of the attacker location on the detection accuracy.
- The simulator will proceed to implement normal RPL behavior, where nodes continuously update their adopted parents by choosing nodes within their communication range with the lowest advertised rank, assuming they are closer to the root. Since the compromised node advertises a rank very close to that of the root node, neighboring nodes will perceive it as offering a shorter path to the root, gradually adopt it as their preferred parent and reroute their traffic accordingly. This marks a successful launch of the sinkhole attack.
- Once the network reconstruction process is finished, the network transitions to the data packet transmission phase. Extensive trial and error experiments have demonstrated that approximately 4 minutes of simulation time (counted from the beginning of step 1) are sufficient to reach this stage. Given that the focus is the detection of the attack using exchanged control packets, further simulation beyond the initiation of data packet transmission is deemed unnecessary. Therefore, it was decided that the simulations would be allowed to run for only 4 minutes.
- After the conclusion of the 4 minutes, Wireshark will be used to collect all of the exchanged packets throughout the simulation time. These packets will be recorded in the Dataset, with their exact sequencing preserved.


Collection of Normal Network Traffic for the UOS_IOTSH_2024 Dataset
A number of network configurations were built in COOJA and allowed to operate normally for 4 minutes (as established in section ‎7) to allow the DODAG construction process to complete and converge. The network traffic under these circumstances forms the baseline normal behavior for the simulated networks. The following subsections will detail the process of collecting the data for each scenario.

Small Single-DODAG Network
A network comprised of a single router (representing the root node) and 12 Tmote Sky Mote nodes (representing the sensor nodes) was constructed in the COOJA simulator. The sensor nodes were arranged in rows with varying distances from the root to encourage a hierarchical DODAG. To prevent potential data bias, the placement of nodes with respect to their node IDs was deliberately randomized.
The simulator was then operated continuously for a duration of 4 minutes of simulation time. After that, Wireshark was used to capture all ICMP4 and UDP packets exchanged during the simulation period. Careful examination of the information in the collected packets showed that the network assembled appropriately.

Medium-sized Single-DODAG Network
A medium-sized network comprised of a single root node and 24 Tmote Sky Mote nodes was constructed in COOJA. The sensor nodes were distributed in a manner similar to the previous network to encourage a hierarchical construction of the DODAG. Then, the same steps were followed for collecting the network traffic from the COOJA simulator. 

Medium-sized Dual-DODAG Network under Normal Operation
In order to facilitate investigating the behavior of a sinkhole attack on a network with multiple DODAGs, we chose to incorporate samples from a dual-DODAG network into the dataset. Accordingly, a medium-sized network comprised of two routers (representing two root nodes) and 24 Tmote Sky Mote nodes was built in COOJA. The nodes were distributed in a manner to encourage the establishment of two DODAGs.
The simulator was allowed to run normally for the 4 minutes, resulting in the nodes automatically organizing into two DODAGs. Each DODAG contained a single root node (node 1 and 14 respectively) and 12 sensor nodes.
 
Collection of Traffic from Networks Subjected to a Single Attacker
The same three combinations of network sizes and structures from the previous section were subjected to a single sinkhole attack to enable the extraction of representative traffic from networks under attack.

Small Single-DODAG Network under a Single Sinkhole Attack
The network was recreated exactly. Then, the procedure described in section ‎7 was followed to simulate compromising node number 2 to launch an internal sinkhole attack on the network at the 100 second mark. Examination of the collected messages reveals that the nodes initially established the same DODAG . However, following the launch of the attack, the DODAG structure underwent significant modification.
To ensure the dataset encompasses a broad range of scenarios with attackers originating from various points within the network, we conducted an exhaustive simulation where a different node was selected as the compromised node for each iteration. As a result, 12 different instances of the Small Single-DODAG Network segment were added under the Single Attacker category of the dataset.

Medium-sized Single-DODAG Network under a Single Sinkhole Attack
The medium-sized single-DODAG network was recreated and simulated with one of the nodes launching a sinkhole attack after 100 seconds of simulation time. Similarly, the simulation was run exhaustively with a different node being selected as the attacking node for each iteration. Twenty-four different instances representing the attack being launched from each of the 24 nodes were added to the dataset. 

Medium-sized Dual-DODAG Network under a Single Sinkhole Attack
A dual-DODAG network essentially consists of two independent small single-DODAG sub-networks. When a single attack is launched on such a network, one DODAG will continue to operate normally, similar to the scenario discussed in section ‎8.1, while the other DODAG behaves as described in section ‎9.1. In such cases, there is nothing particularly noteworthy about the samples extracted under these conditions.
On the other hand, if the attack is initiated from a node on the border between the two DODAGs, significant variation may occur in the traffic patterns. Here the attack may alter the distribution of the nodes between the two DODAGs and attract traffic from nodes that were previously in the other DODAG.

Accordingly, the network was recreated and simulated with an attack being launched using one of the border nodes after 100 seconds of simulation time. The simulation was repeated exhaustively for each of the border nodes. This resulted in eight different instances being added to the dataset representing the traffic streams collected from each border node in the network.

Collection of Traffic from Networks Subjected to Dual Attackers
A network may be infected by multiple attackers at the same time. Therefore, we created a category in the dataset to represent scenarios where the network is attacked by two attackers. It is highly unlikely that the two attacks will be launched simultaneously. Therefore, our simulations consider that one of the nodes launches an attack at the 100 second mark, while the second node launches the attack 50 seconds later. 
The same network structures described in the previous two sections were subjected to two attackers to enable extracting the representative samples for the dataset.

Small Single-DODAG Network under a Dual Sinkhole Attack
The network was recreated and simulated with two nodes launching sinkhole attacks. Rather than attempting an exhaustive simulation of all the possible combinations of two attacking nodes in a network of 12 nodes (132 combinations), we used the Sinkhole Attack Effect measure to evaluate different scenarios involving two attackers at different location in the network to determine the combinations producing the worst-case effect.
In the first step, an exhaustive analysis was conducted for the sinkhole attack effect of a single attack at each of the nodes in the network. These scores were used to gauge the correlation between the topological level of the attacker and the effect of an attack from that level. The results show that an attack from level 1 would produce the worst effect due to the proximity to the root nodes.

It was noticed that the attack effect dropped significantly when the attacking node was in level 2 compared to when it was in level 1. On the other hand, the attack effect increased with the increase in the topological level of the attacker. This can be attributed to the fact that nodes in level 1 will be little affected by an attacker at a more distant level, and will maintain their parent selection integrity. Consequently, nodes at level 1 remain unharmed if the attacker is positioned at, for instance, level 2. However, as we move to levels further from the root, nodes positioned beyond the transmission range of the root become more susceptible to the influence of the attacker.

Based on these evaluations, it is determined that having two attackers in level 1 of the DODAG would be totally catastrophic to the network and would not offer any insight on the behavior of the IDS under the attack scenario. Therefore, this combination was not included in favor of more interesting and challenging combinations.

On the other hand, the results demonstrate that certain combinations of attack locations produced significant combined sinkhole attack effect scenarios. Therefore, based on these considerations, we decided that we would include only the following representative combinations in the UOS_IOTSH_2024 Dataset:
- One attacker at L2 and another attacker at L3: Node 8 and Node 2
- One attacker at L2 and another attacker at L3: Node 11 and Node 12
- One attacker at L2 and another attacker at L4: Node 5 and Node 9
- One attacker at L3 and another attacker at L4: Node 5 and Node 12

Simulations were carried out for these scenarios and the network traffic was collected in a manner similar to what was discussed in earlier steps. Four instances were added to the dataset to represent these four scenarios.

Medium-sized Single-DODAG Network under a Dual Sinkhole Attack
The network was recreated and simulated with two nodes launching attacks with a 50 second interval. The attack effect score was employed to evaluate different scenarios involving two attackers in diverse network positions to determine the combinations producing the most severe attack effects.
Simulations were carried out for the scenarios and the network traffic was collected in a manner similar to what was discussed in earlier steps. Five components were added to the dataset to represent these five scenarios.

Medium-sized Dual-DODAG Network under a Dual Sinkhole Attack
The network was recreated and simulated with two nodes launching a sinkhole attack. Given that this network topology is a combination of two small networks, therefore, investigating the situation of both attacks being launched in the same DODAG will devolve into the same behavior investigated in section ‎10.1. Therefore, the dual attack simulations for this setup were implemented by initiating each attack in a separate DODAG. 

The following four representative combinations were selected through informed guessing:  
- Node 7 (DODAG 1) and Node 26 (DODAG 2)
- Node 17 (DODAG 2) and Node 12 (DODAG 1)
- Node 8 (DODAG 1) and Node 19 (DODAG 2)
- Node 10 (DODAG 1) and Node 20 (DODAG 2)

Then the network traffic was collected in a manner similar to what was discussed in earlier steps. Four components were added to the dataset to represent these four scenarios. 

Complete composition of the UOS_IOTSH_2024 Dataset
Once the samples for all of the different scenarios were collected, we ended up with a dataset comprised of 9 different categories covering different network sizes, different network topologies, and different attacker counts. In total, the dataset covers 57 different attack scenarios, and includes more than one million samples of network traffic as shown in the following Table.

Composition of the UOS_IOTSH_2024 Dataset

-------------------------------------------------------------------------------------------------------------------------
Component								| No. of	| No. of 	| No. of 	|
									| Scenarios per | Samples per 	| Samples per 	|
									| Component	| Scenario	| Component	|
-------------------------------------------------------------------------------------------------------------------------
Small Single-DODAG Network under Normal Behavior			| 1		| 5,923		| 5,923		|
Medium-sized Single-DODAG Network under Normal Behavior			| 1		| 36,759	| 36,759	|
Medium-sized Dual-DODAG Network under Normal Behavior			| 1		| 16,699	| 16,699	|
Small Single-DODAG Network under a Single Sinkhole Attack		| 12		| 5,582		| 66,984	|
Medium-sized Single-DODAG Network under a Single Sinkhole Attack	| 24		| 37,342	| 896,208	|
Medium-sized Dual-DODAG Network under a Single Sinkhole Attack		| 8		| 16,743	| 133,944	|
Small Single-DODAG Network under Dual Sinkhole Attacks			| 4		| 5,767		| 23,068	|
Medium Sized Single-RPL Network under Dual Sinkhole Attacks		| 5		| 36,373	| 181,865	|
Medium Sized Dual-DODAG Network under Dual Sinkhole Attacks		| 4		| 17,449	| 69,796	|
-------------------------------------------------------------------------------------------------------------------------
Total									| 60		|		| 1,431,246	|
-------------------------------------------------------------------------------------------------------------------------

Data Preprocessing for the UOS_IOTSH_2024 Dataset
Some of the features mentioned in Table 2 required some preparatory modifications before they can be used in training of ML and DL models. Consequently, the following pre-processing steps were applied to all extracted packets before being included in the dataset:
- Conversion of the hexadecimal values in the “source IP address” and “destination IP address” columns using one-hot encoding to enable the ML/DL models to learn distinct patterns linked to each IP address.
- Encoding “Info” column which displays the message type (DIO, DAO, DIS, and ACK) in string format. The same step is also performed to the “Protocol” column that determines the protocol type (ICMPv6, UDP, and IEEE 802.15.4). The different string values were encoded to enable the ML and DL models to understand their values.

These steps did not affect the content of the fields, but rather changed the representation of the data into a format better understandable by the modeling environment.
Sample Labelling for the UOS_IOTSH_2024 Dataset
The sinkhole attack disrupts the network topology causing effects possibly reaching very far across the network. Accordingly, it is possible that the entire network traffic may be affected after the attack. Therefore, one should consider the traffic of the entire network while trying to detect the attack. Based on this understanding, a labelling approach based on the status of the entire network rather than a specific node was chosen. A ‘network operating normally’ label was applied to all of the traffic in the dataset ‘before’ the attack is launched, and a ‘network under attack’ label to all of the traffic ‘after’ the attack is launched. This labelling approach will enable the detection of anomalies in the network behavior caused by the attack, but will not enable identifying the malicious node specifically.

The sample distribution is slightly unbalanced favoring ‘normal’ samples. This can be attributed to the fact that more traffic is exchanged during the early stages of the DODAG construction (the first 100 seconds) compared to the amount exchanged during the DODAG update once the attack is launched (after the first 100 seconds).
 
References
[1]	Raza S, Wallgren L and Voigt T 2013 SVELTE: Real-time intrusion detection in the Internet of Things Ad Hoc Networks 11 2661–74
[2]	Mirshahjafari S M H and Ghahfarokhi B S 2019 Sinkhole+CloneID: A hybrid attack on RPL performance and detection method Inf. Secur. J. 28 107–19
[3]	Bostani H and Sheikhan M 2017 Hybrid of anomaly-based and specification-based IDS for Internet of Things using unsupervised OPF based on MapReduce approach Comput. Commun. 98 52–71
[4]	Alzubaidi M, Anbar M, Chong Y W and Al-Sarawi S 2018 Hybrid monitoring technique for detecting abnormal behaviour in rpl-based network J. Commun. 13 198–208
[5]	Taghanaki S R, Jamshidi K and Bohlooli A 2019 DEEM: A decentralized and energy efficient method for detecting sinkhole attacks on the internet of things 2019 9th Int. Conf. Comput. Knowl. Eng. ICCKE 2019 2019 325–30
[6]	Yang W, Wang Y, Lai Z, Wan Y and Cheng Z 2019 Security Vulnerabilities and Countermeasures in the RPL-Based Internet of Things Proceedings - 2018 International Conference on Cyber-Enabled Distributed Computing and Knowledge Discovery, CyberC 2018 (Conf. Cyber-Enabled Distrib. Comput. Knowl. Discov. CyberC 2018) pp 49–54
[7]	Muzammal S M, Murugesan R K, Jhanjhi N Z, Humayun M, Ibrahim A O and Abdelmaboud A 2022 A Trust-Based Model for Secure Routing against RPL Attacks in Internet of Things Sensors 22
[8]	Airehrour D, Gutierrez J A and Ray S K 2019 SecTrust-RPL: A secure trust-aware RPL routing protocol for Internet of Things Futur. Gener. Comput. Syst. 93 860–76
[9]	Napiah M N, Bin Idris M Y I, Ramli R and Ahmedy I 2018 Compression Header Analyzer Intrusion Detection System (CHA - IDS) for 6LoWPAN Communication Protocol IEEE Access 6 16623–38
[10]	Patel B and Shah P 2021 Direct Neighbour Sink Reputed Trust Based Intrusion Detection System to Mitigate Sinkhole Attack in RPL for IoT Networks J. Eng. Sci. Technol. Rev. 14 38–45
[11]	Cervantes C, Poplade D, Nogueira M and Santos A 2015 Detection of sinkhole attacks for supporting secure routing on 6LoWPAN for Internet of Things Proceedings of the 2015 IFIP/IEEE International Symposium on Integrated Network Management, IM 2015 (Symp. Integr. Netw. Manag. IM 2015) pp 606–11
[12]	Tabari M Y and Mataji Z 2020 Detecting Sinkhole Attack in RPL-based Internet of Things Routing Protocol J. AI Data … 9 73–85
[13]	Surendar M and Umamakeswari A 2016 InDReS: An Intrusion Detection and response system for Internet of Things with 6LoWPAN Proceedings of the 2016 IEEE International Conference on Wireless Communications, Signal Processing and Networking, WiSPNET 2016 (WiSPNET 2016: Conf. Wirel. Commun. Signal Process. Networking) pp 1903–8
[14]	Zaminkar M, Sarkohaki F and Fotohi R 2021 A method based on encryption and node rating for securing the RPL protocol communications in the IoT ecosystem Int. J. Commun. Syst. 34 1–18
[15]	Zaminkar M and Fotohi R 2020 SoS-RPL: Securing Internet of Things Against Sinkhole Attack Using RPL Protocol-Based Node Rating and Ranking Mechanism Wirel. Pers. Commun. 114 1287–312
[16]	Choudhary S and Kesswani N 2019 Cluster-based intrusion detection method for internet of things Proceedings of IEEE/ACS International Conference on Computer Systems and Applications, AICCSA vol 2019-Novem (vol. 2019-Novem: Conf. Comput. Syst. Appl. AICCSA) pp 1–8
[17]	Farooq U, Asim M, Tariq N, Baker T and Awad A I 2022 Multi-Mobile Agent Trust Framework for Mitigating Internal Attacks and Augmenting RPL Security Sensors 22
[18]	Prathapchandran K and Janani T 2021 A trust aware security mechanism to detect sinkhole attack in RPL-based IoT environment using random forest – RFTRUST Comput. Networks 198
[19]	Kamel S O M and Elhamayed S A 2020 Mitigating the impact of iot routing attacks on power consumption in iot healthcare environment using convolutional neural network Int. J. Comput. Netw. Inf. Secur. 12 11–29

