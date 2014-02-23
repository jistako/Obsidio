Obsidio
=======

Sustainable P2P storage of arbitrary data in an ever-branch-able block-tree.


Overview
--------

Obsidio is a protocol that can be used to produce a peer-to-peer network for the storage of blocks of arbitrary data (e.g. text, code, raw binary) in an ever-branch-able tree with an integrated payment system.  The network protocol is copied from Bitcoin.  The tree consists of an ever-branchable block-tree all rooted on a single genesis block.  Lastly, the payment system is designed to incentivize users to upload blocks and nodes to store as much of the tree according to the demand of users.

Each part of the potocol is described in the relevant section.


Applications
------------

The protocol does not necessitate anything but that the blocks are contructed properly, thus Obsidio can be used for many types of applications.  However, the following are some ideas may qualify as interesting use-cases of Obsidio:

 * Microblogging - Send blocks to the network and receive funds as people download your microblog blocks.
 * Alternate DNS - Store your website's URL in a block.  Update your record it by posting another block imediately after that initial one to indicate the IP address change.
 * Public-key distribution
 * Bot-net control - Command-and-control messages could be stored in a particular branch to which the bots listen, though the price for retrieving each message might make it cost-prohibitive for bot-nets beyond a few dozen bots.
 * Code storage/distribution - Obsidio could replace, at least for smaller projects, the use of Git and similar.


Block Tree
----------

Whereas Bitcoin stores the account ledger in the form of a single block-chain, Obsidio stores all of the data in a block-tree.  Bitcoin needs a single chain of blocks in order to affirm that there is a single unambiguous account of all transactions; Obsidio simply handles the blocks and does nothing with the data therein, so there is no need for a single authoritative chain of blocks.  Every node must store at least the very first block in the tree, thus nodes are free to provide as much or as little of the tree as each chooses.

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

Every block must consist of a hash of the rest of block, a target block hash, a public key, a signature of the arbitrary data, and the arbitrary data itself:

    hash ( target-hash + public-key + signature ( data ) )

The target is simply the hash of the block with which you want your new block to be associated in the block-tree.  The public-key and signature are meant to make authentication possible for each block; we use Xxxx.  The data can be anything you like, but it must be between 1 byte and 1 megabyte in length, inclusively.  Once you have it all assembled, you hash the resulting string and send upload it to a node.


Growing the Tree
----------------

In order to add a block to the block-tree, one simply sends a properly formed block and payment to at lest one node, though multiple nodes would be appropriate; the reference implementation will help with this.  The contacted nodes then respond with either an affirmative "your block has been added immediately after [insert target hash], reason [code#]" or a negative "you block could not be added, reason [code#]".  If it succeeded, then both your payment is processes and your block has been added to the branch of the tree; you might consider sending it to more than a few nodes, but the more will cost you proportionately so.


Reason-codes
------------

If, for some abominable reason, your particular Obsidio implementation does display reason codes, instead of interpreting them for you, then you can refer to the following list for an explanation as to what has likely occured:

    # = Reason
    0 = Block accepted, and payment was received properly, if any was required.
    1 = Block was rejected, because it is malformed.
    2 = Block was rejected, because insufficient payment was included in request.
    3 = Block was rejected, because node does not have target block and can't/won't get it.


Watering the Tree
-----------------

As sustainability is appropriate for achieving a sizeable peer-to-peer network, payments are supported by default, but not required.  Thus, there may persist a set of nodes that provide the block-tree freely, though inter-node traffic will have to be paid for by some means.

When a user seeks to upload a block, he must pay the node according to the amount as advertised in the node's catalogue.  When a second node or user requests that block from the first node, the second node must pay to download the block, but half of the payment goes to the user and half goes to the node; negotiation of that ratio may implemented later.  Thus, the end-user will ultimately pay for the uploading of the blocks and the maintenance of the network, assuming nodes adjust their prices properly.


Block Catalog
-------------

In order for payments to be possible, a catalogue, of sorts, is provided by every node containing the beginnings and ends of whatever the node has and the price ratio to receive those blocks.  To prevent silly denial of services, the reference implementation, used by nodes, will simply not respond to repeated requests for catalogues, since they are, generally, available at no charge.  That being said, a node could demand payment for catalogs, if it were so inclined.


