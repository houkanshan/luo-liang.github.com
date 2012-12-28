---
date: 2012-11-10 17:53:10
title: Wordament in Chinese PART2
layout: post
tags:
    - SILVERLIGHT
categories:
    - Dev
---
I would first love to congratulate myself on ranking in the top 23% of players in Wordament. Then, I would make a correction to an assert about Wordament's sending words found to servers to do the verification I made in Part 1 of this article. I believe this is not always the case since the best strategy would be to store part of the most common words locally to save data connection bandwidth.

Last time our Wordament game left us designing an efficient dictionary index. We have analyze the inefficiency of tree structure in dealing with indexing English words.

The proposed solution would have at least 2 features：On one hand, we take advantage of tree structure, sharing common structures as many as we can. On the other hand, we try best to eliminate waste.

	1)Sort all words alphabetically.


	2)Set up a gauge. If a common structure is shorter in length than the gauge, the common structure is not shared, otherwise, it is shared. To determine the exact value of the gauge, consider using the CPU architecture as a factor. If we are working on an X86, we would prefer to set gauge larger than 8; if we're working on an x64, we would prefer gauge to be larger than 16. Or, the pointer size itself will offset the memory we thrifted via sharing. Now suppose we set gauge to 8.


	3)Foreach word, maintain a linked list, within the node content of the linked list, a string field and a pointer to another node are established. This gives each linked list node 3 fields: a Next pointer, a pointer to another(unknown as of now) node, and a string field.


	4)List all words alphabetically whose length is less than gauge. This can be done in a simple while loop. We name this list "done list" and add the word itself to the linked list. Like this:


	WORD 				REP
	a			[Null,Null,"a"]
	ad			[Null,Null,"ad"]
	...
	anti			[NULL,Null,"anti"]
	...
	international		[Null,Null,"international"]


	5)Foreach remaining word, use Matrix X Matrix (We can also use KMP)method to compare it with all the words in the done list and find the maximum common strings. Keep finding and connecting these strings until we exhaust the word. For example, when examining the word "internationalism", the word "international" should be already in the done list. Our algorithm would find the longest match would be "international". We then represent "internationalism" as:

	[NEXT,Ptr to "international",Null]->[Null,Null,"lism"]

	and then, when the word "anti-internationalism" comes, we can save it as
	[NEXT,Ptr to "Anti",Null]->[Null,Ptr to "Internationalism",Null]

	The whole Step 4 will be the hotspot for performance tuning, as the overall complexity will be O(n^3).

	5)Exhaust all words by repeating Step 4.

We don't need to worry about the extra complexities, as this index will be done only once. The problem is that when a new word is added, another O(n^2) will be spent.

Now let's consider the saving of memory. Suppose again we have 1 million words. Each linked list contains at least 1 node and should contain no more than 3 (we don't have many words that consist of 40 letters). Each string field has a length of 5 (the average of word we assumed in Part I of this article, we have made it much worse than reality because we assume each linked node has a non-null string, as this is not true), here we temporarily ignore the "gauge" thing (As before, suppose we are working on x64 and we are using native language so strings are stored within the structure itself).

	The overall memory consuption would be:
	1 Millon nodes * 3 * (5 + 8 * 2) = 60MBytes

This would be a great save in comparision with the tree structure. Notice the length of the linked list would be much less than 5 as we are really considering the worst case situation here.

There is always an possibility to make this even better. By properly design the link node structure, many useless links can be eliminated and thus results in more memory savings.


What about search efficiency?
By properly arranging the list, we can achieve at least O(logN) speed, ignoring memory redirection cost. By properly hashing the list, we can achieve O(1).