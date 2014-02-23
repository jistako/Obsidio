Obsidio
=======

Sustainable P2P storage of arbitrary data in an ever-branch-able block-tree.


Overview
--------

Obsidio is a protocol that can be used to produce a peer-to-peer network for the storage of blocks of arbitrary data (e.g. text, code) in an ever-branch-able tree with an integrated payment system.  The network protocol is copied from Bitcoin.  The tree consists of an ever-branchable block-tree rooted on a single genesis block.  Lastly, the payment system is designed to incentivize users to upload blocks and nodes to store as much of the tree according to the demand of users.


Applications
------------

The protocol does not necessitate anything but that the blocks are contructed properly, thus the usage of Obsidio can be used for many types of applications.  However, the following are some ideas may qualify as interesting use-cases of Obsidio:

 * Microblogging - Send blocks to the network and receive funds as people download your microblog blocks.
 * Alternate DNS - Store your website's URL in a block.  Update your record by posting another block imediately after that initial one to indicate the IP address change.
 * Public-key distribution
 * Bot-net control - Command-and-control messages could be stored in a particular branch to which the bots listen, though the price for retrieving each message might make it cost-prohibitive for bot-nets beyond a few dozen bots.
 * Code storage/distribution - Obsidio could replace/supplement, at least for smaller projects, the use of Git and similar.  I will endeavor to store the code for the reference implementation of Obsidio in the blck-tree.


Block Tree
----------

Whereas Bitcoin stores its account ledger in the form of a single block-chain, Obsidio stores all of the data in a block-tree.  Bitcoin needs a single chain of blocks in order to affirm that there is a single unambiguous account of all transactions; Obsidio simply handles the blocks and does nothing with the data therein, so there is no need for a single authoritative chain of blocks.  Every node must store at least the very first block in the tree, thus nodes are free to provide as much or as little of the tree as each chooses.

    [g] = genesis block
    [2] = block 2 in that branch
    -> = add
    / \ = branch
    ... = continues into the future
  
	    ->[1]->[2]->[3]->...
	   /
	[g]->[1]->[2]->[3]->[4]->[5]->...
		\	  \
		 \	   ->[4]->[5]->...
	      \
		   ->[2]->[3]->...

Every block must consist of (1) a hash of the rest of block,(2) a target block hash, (3) a public key, (4) a signature of the arbitrary data, and (5) the arbitrary data itself:

    Hash ( Target-hash + Public-key + Signature ( Data ) )

The target is simply the hash of the block with which you want your new block to be associated in the block-tree.  The public-key and signature provide authentication for each block.  The data can be anything you like, but it must be between 1 byte and 1 megabyte in length, inclusively.  Once one has the block assembled properly, one uploads it to a node along with a payment.


Growing the Tree
----------------

In order to add a block to the block-tree, one simply sends a properly formed block and payment to at least one node, though multiple nodes would be appropriate; the reference implementation will help with this.  The contacted nodes then respond with either an affirmative "your block has been added immediately after [insert target hash], reason [code#]" or a negative "you block could not be added, reason [code#]".  If it succeeded, then both your payment is processed and your block has been added to a branch of the tree; one might consider sending it to more than a few nodes, but the more will cost you proportionately so.


Watering the Tree
-----------------

As sustainability is appropriate for achieving a sizeable peer-to-peer network, payments are supported by default, but not required.  Thus, there may persist a set of nodes that provide the block-tree freely, though inter-node traffic will have to be paid for by some means.

When a user seeks to upload a block, he must pay the node according to the amount as advertised in the node's catalogue.  When a second node or user requests that block from the first node, the second node must pay to download the block, but half of the payment goes to the user and half goes to the node; negotiation of that ratio may implemented later.  Thus, the end-user will ultimately pay for the uploading of the blocks and the maintenance of the network, assuming nodes adjust their prices properly.


Block Catalogue
-------------

In order for payments to be possible, a catalogue, of sorts, is provided by every node containing the beginnings and ends of whatever the node has and the price ratio to receive those blocks.  To prevent silly denial of services, the reference implementation, used by nodes, will simply not respond to repeated requests for catalogues, since they are, generally, available at no charge.  That being said, a node could demand payment for catalogues, if it were so inclined.


