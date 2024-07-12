This is how things are organized/how the playbooks are meant to work.

----Inventories----
1-hosts contains a list of hosts along with the address and port to reach them
2-groups is where these hosts are grouped according to their development stage, architecture, and role.
	k8scp is for nodes that only operate as a control plane
	k8swn is for nodes that only operate as a worker node.
	k8shy is for nodes that can do both.
	nfshost is for the nfs server
	amdpc and armpc are groups for server architecture (arm are usually raspberry pis)
	hardening>hardened>zsh_setup>k8s_setup Are the groups for setting up new nodes.
	k8scluster contains all kubernetes nodes, and setup contains all the new nodes.

----Variables----
- group_vars/all - contains variables for the IP pool that allows for easy SSH access, and the nfs_storage location. There might be other globals too in the future.
- group_vars/k8scluster - contains kubernetes cluster specific variables, like the initial control plane and the version of the cluster to install to.
Each host has an individual <hostname>.yml file containing the ansible_user to access the host and all the colors for configuring starship.

----Playbooks----
	hardening
- harden1.yml - Begins the hardening process, installs most softwares and sets time. Manually set up the google authenticator after running this.
- harden2.yml - Finishes up the hardening process. Does most of the configuration work.
	hardened
- nfs-setup.yml - Sets up the nfs-server and configures it to be reachable on the network.
- zsh-setup.yml - Configures the node with zsh and starship.
	zsh-setup
- k8s-setup.yml - Sets up Kubernetes on a node and gets it ready to join the cluster.
	k8s-setup & k8s-cluster
- k8s-join.yml - Joins a node to the Kubernetes cluster, Be sure to assign it to a group under k8s-cluster!
- k8s-update.yml - Performs a rolling update over the whole Kubernetes cluster.