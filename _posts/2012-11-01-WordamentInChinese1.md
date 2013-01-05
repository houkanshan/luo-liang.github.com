---
date: 2012-11-01 17:53:10
title: Wordament in Chinese PART1
layout: post
tags:
    - SILVERLIGHT
categories:
    - Dev
---

One of the funniest games in XBOX live for me is the Wordament, where a 4x4 box of random letters are displayed. Players are asked to swipe their mouses/fingers across these letters in a correct order to reveal an English word. Each word a player find will carry some value points, and typically, the longer word you find, the higher value you'll get. At the end of each round, players from all over the world will be ranked according to their total points earned. Wordament™ automatically synthesizes the 4x4 box and sync players to challenge the same box about every 3.5 minutes. Players around the World can join in the game at any time. 

Wordament is a good place where you wish to train your abilities in observation, detection and reaction. But being almost always ranked below 40% really pissed me off. 

To change this situation, the best way is to devise a Wordament in Chinese, which I'm pretty sure that I will have more edge.
## Overall Designs ##

To make our game available to mobile platforms, we need to make sure the size of the game is small. 

When the user is swiping across the screen, we should send the word to the server for processing. This brings 2 benefits:
 
- users do not need dictionary packages
 
- updates to dictionary is transparent

but this also brings some challenges. For example, should we freeze the UI if the network connection is slow? We should probably do this because users really care about if the word they found exists, so if nothing is prompt after they have swiped across the board, they tend to keep swiping until a visual cue is displayed. And this is what the original Wordament game does, so we should probably learn from what is already right.

The English words are made of characters. Chinese characters are made of strokes. Like English words, there are lot of common letter/strokes between 2 different characters. It is the combination of different strokes that makes lots of characters. We can learn from the famous 9-key Chinese input method on our smartphones that only 5 basic strokes are needed to "spell" all the Chinese characters. 

HENG, SHU, PIE, DIAN and HENG ZHE

These five different strokes are like a-z in English.

Each Chinese character has only one way to write, much like each word in English has only one correct way to spell. For example, the only correct way to spell "DA"(or big in English), is "HENG" + "PIE" + "DIAN".

But there is an essential difference between these 2 languages. We can think of English words appearing on the number axis, your reading direction is from minus infinity to infinity. However, in Chinese, the characters appear in a 2D space, which means the placement of strokes matters in determining the characters spelled. For example, beside "DA", "HENG" + "PIE" + "DIAN" can also represent "ZHANG"(an ancient measuring unit in China).

 This gives us an interesting question. If "DA" is spelled and eliminated by "HENG" + "PIE" + "DIAN", should "ZHANG" be eliminated too? The answer will probably be yes, or the user will be swiping across the same strokes until all candidates made of those strokes are eliminated which makes the game lame.

## Designing the Index ##

English has about 1 million words while Chinese has only 100K. The traditional way to use a tree to represent words in common dictionaries might no longer be applicable if we are playing with the whole set of words as it will be memory consuming.

Let's do a series of assumptions: 

- Average English word length is 5. Suppose the 1 million words are continuous, i.e., from a to zzzzz, each variation in letter results in a different word(this is not the case but we can find the minimum average). So this will reduce the question of finding x that makes 26^x = 1 Million. We can easily see x is larger than 4 but less than 5.
- Each tree node has 26 children, at least.

- Tree has 1 million nodes in total.

Now let's calculate the estimated memory consumption. We assume that we use native languages so no overhead for `class`. In this situation, each letter takes up 1byte. Again, let's assume the average tree height is 6 (plus the root), since we have an average length of 5 among the words. To assist in calculation, we make 26^5 1 million. At last, we assume we are using x64 and the array and string are stored within the structure itself, so no overhead for the pointer to the array of child pointers nor the string of the node.

    1 Million word * 5 letter/each word * 1byte/letter = 5 Million bytes = 4.76MBytes
    
    1 Million nodes * (26) pointers/each node * 8byte/pointer = 198MBytes
    
    SUM 203MBytes
    
The real situation will be much worse, because here we assume words are continious, which means if abc exists, abcx (x belongs to a-z) exists, which is not true at all. This will be a huge waste, as huge numbers of pointers will point to null because many words simply don't exist. Also, unnecessary wastes occur because the reuse rate in English language is amazingly high.

    inter
    nation
    national
    international

The tree structure can effectively reuse common prefixes, but the tree itself is not enough to reuse common suffixes, especially those compound words like `international`, which is effectively composed of `inter` and `national`.
