MinHash
=======

This project demonstrates using the MinHash algorithm to search a large 
collection of documents to identify pairs of documents which have a lot of
text in common.

This code goes along with a tutorial on MinHash on my blog, here:
https://chrisjmccormick.wordpress.com/2015/06/12/minhash-tutorial-with-python-code/

The code includes a sample dataset of 10,000 articles containing 80 examples of
plagiarism. That is, there are 80 articles in the dataset which are identical
or nearly identical to another article in the dataset. 

I've also included smaller subsets of the data that you can experiment with, 
since the full 10,000 articles can take a while to process. By default, the code
points to a subset of 1,000 articles so that it runs quickly. 

I found that computing the Jaccard similarity explicitly between all 10,000 
articles requires 20 minutes on my PC, but doing it with MinHash requires a 
little under 3 minutes.


Part I: Preliminaries

  Part IA: Dataset parsing
      1-Write a function parse_data that given the path to a filename, reads in the article data and returns an array of tuples. With
      2-One tuple per article (there is one article per line)
      3-For each article tuples will contain (id, string) where id is the article id and string is the article text as described next
      4-Process the article text to
      5-remove all punctuation
      6-change all letters to lowercase
      7-remove all whitespace so that all words are concatenated

  Part IB: Document shingles

      1-Write a function shingle_document that given a processed article string and a parameter k shards the document as follows:
      2-each substring of length $k$ in document is hashed to a 32-bit integer (see crc32 fucntion in https://docs.python.org/3/library/binascii.html)
      
     3- returns a list of the unique 32-bit integers obtained in previous step (look at python sets for this)
    
  Part IC: Jaccard Similarity
      Write a function jaccard that given two sharded documents, computes their Jaccard distance
      
  Part ID: Put these together
    1-Write a function that uses the above to do the following:
    2-Parse a file with data
    3-Return a list of tuples each tuple contains: (id1, id2, s), where id1 and id2 are document ids and s is the computed Jaccard similarity
    
  Part IE: Experiment 0
    1-Use your function to answer the following question:
    2-What is the effect of sharding length `k** on the Jaccard similarity of plagiarism instances versus instances that are not plagiarized?
    3-To answer this question, make a plot with $k$ in the x-axis and average Jaccard similarity in the y-axis. Plot two lines, one line for plagiarism instances, one line for instances that are not plagiarized. Use the 1000 document dataset for this.
    
    
Part II: MinHash
In this section you will implement the MinHash algorithm and perform an experiment to see how well it estimates Jaccard similarity.
  Part IIA: Prepare shingles for processing
      Implement a function that takes the shingled documents and returns a list of item-document pairs sorted by items that we’ll use to compute the minhash signature of each document. Remember that because of the shingling logic we used above, we represent items as 32-bit integers. The function specs are as follows:
      Input is a list of tuples of form (docid, [items])
      Output will be a tuple with two elements:
      a list of tuples of form (item, docid). It will contain one entry for each item appearing in each document.
      a list of document ids found in the dataset
      Output should be sorted by item
 
  Part IIB: Generate hash functions
    Use the generate_random_hash_fn function below to create function make_hashes. Given input num_hashes this function will return a list of hash functions that mimic the random permutation approach used in Minhash calculation. The function specs are:
    Input is an integer num_hash
    Output is a list of hash functions created by function generate_random_hash_fn
  
  Part IIC: Construct the Minhash Signature Matrix
    Implement a function that builds the Minhash signature matrix. You can use this code as a starting point. It refers to the functions you implemented above and sketches the construction algorithm.
    
  Part IID: MinHash similarity estimate
    Write a function that computes the similarity of two documents using the minhash matrix computed above. The function specs are:
    Input:
    id1, id2: document ids
    minhash_sigmat: minhash signature matrix
    docids: list of document ids, used to index the columns of the minhash signature matrix
    Output: Jaccard similarity estimated using minhash
    
  Part IIE: Put these together
      Write a function that given shingled documents computes the Minhash estimated similarities between each pair of documents. This will be similar to your function for Part ID.
  Part IIF: Experiment 1
      Use your function to carry out the following experiment:
      What is the effect of the number of hash functions used to compute the Minhash signature on the accuracy of the Minhash estimate of Jaccard similarity. Carry out this experiment on the 1000 document dataset.
      
      
Part III: Locality-Sensitive Hashing

Implement LSH
    -Write a function that implements locality sensitive hashing. Function specifications:
    Input:
    minash_sigmatrix: a minhash signature matrix
    numhashes: number of hash functions used to construct minhash signature matrix
    docids: list of document ids
    -threshold a minimum Jaccard similarity threshold
    Output:
    a list of hash tables 

    -Find candidate similar article pairs
    -Write a function that uses the result of your LSH function and returns list of candidate article pairs. Spec:
    Input: the result of do_lsh
    Output: list of tuples (docid1, docid2) each a candidate similar article pair according to LSH
  
  Experiment 2: LSH sensitivity
    Use these functions to compute the sensitivity and specificity of LSH as a function of the threshold. Use the 10,000 document dataset to perform this experiment.
