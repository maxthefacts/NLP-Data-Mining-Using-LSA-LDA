# Consumer-Fraud-Detection-with-Tweets-
=======
# NLP-Data-Mining-Using-LSA-LDA

## Executive Summary

This project was performed at the behest of the New York State Office of the Attorney General Consumer Fraud Division.  The New York State Office of the Attorney General (OAG) is the branch of state government entrusted to regulate and enforce consumer protection statutes passed by the state legislature.  Current enforcement investigations begin with leads generated from written complaints submitted by the People of New York to the OAG.  These complaints primarily take two forms, written submissions and online submissions.  In either case, the person submitting the complaint must be savvy enough to know that the OAG is the right place to file the complaint, have access to resources to complies the required information to the complaint, and the confidence in the institutions of government to believe their complaint will be treated appropriately.  These de-facto requirements drastically limits and biases the complaints received by the OAG.

The OAG would like to expand its complaint database as well as the community complaining about fraud by identifying potential complaints being aired over social media, especially Twitter.  To do so, the OAG will need to data mine the extremely large number of tweets created by New Yorkers to identify potentially relevant fraudulent actors.  The CUSP team has developed a data mining approach that begins with publically available tweets and ends with labeled and sorted tweets available to OAG attorneys.

## Goals of the Project

This project used Latent Symantec Indexing and Latent Dirichlet Allocation to infer topicality and similarity between documents in a corpus of text.  Here, the corpus of text is the Consumer Financial Protection Bureau’s publically available database.  This database contains nearly 75,000 narrative descriptions of fraud along with labeled aspects of the alleged fraud, such as the company name.

The goal of the project is to take the Office of the Attorney General’s access to to the twitter ‘firehose’ and see which tweets are similar in meaning to the descriptions of fraud found in the database.  This github repository uses publically available data, the actual project as implemented within the OAG uses a proprietary corpus of fraud descriptions from internal government records.

## Machine Learning

To use unsupervised learning to identify the topics associated with a given entry to the database, the first step is to tokenize each word in each document.  At the same time, common stop words are dropped from the corpus.  Special conditions, such as redacted information, numbers, and dollar values are also removed.  Next, all words that occur less than ten times throughout the corpus are dropped.  Bigrams, collections of two words that occur frequently together, are created.  The threshold for bigram frequency is arbitrarily set at twenty.  A dictionary is then created with a unique integer assigned to each word in the corpus.  Using the CFPB database, this equates to a dictionary with 16,629 unique tokens (words and bigrams).

## word2vec

Next, a word2vec model was created with the python module Gensim.  This model creates a multidimensional vector space.  It assigns each word a location in this space based on its frequency of occurrence, in both individual documents as well as the whole corpus, as well as is the co-occurrence of neighboring words.  By selecting a word in the vocabulary of the remaining corpus, one can find a measure of similarity by looking at the cosine of the angle that results from the selected word and all other words.  Presumably, the cosines closest to one indicates that the word occupies a space similar to the word in question.  A cosine of zero indicates the inverse, a word that occupies a different space.  By finding the word with the maximum cosine value, one can identify through unsupervised learning “similar” words.  By conjoining this similarity “score” one can also identify the firms from the labeled CFBP database that are most associated with certain vocabulary words.  

## LSI Topics

However, we want to compare new documents outside the corpus, in this case tweets, to the existing corpus.  To do that we need to transform the vectors.  First, a bag of words vector representation is created.  Here each unique dictionary key is represented as a sparse vector with its frequency of occurrence in the document, throughout the corpus.  Next, the Gensim tfidf function is used to normalize these frequency scores between zero and 1, based on how often the word occurs in the document as well as throughout the corpus.  The normalized sparse vectors are then transformed using Gensim’s lsi algorithm.  A term-document matrix is constructed with words as rows and documents as columns.  Singluar value decomposition is used to bring the matrix down into manageable dimensions while maximizing preserved information.  The resulting vector space then provides what can be called ‘topics’, or collections of words that describe most of our information.  Here, one can input a document and discover what words best describe the location of that document in the higher dimensional space.  These words can be understood to be ‘topics’.

## LSI Similarity

To identify similarity, the new document, in this case a tweet, is transformed into the same format as the rest of the documents.  It is then added into the multidimensional vector space like the previous documents.  The cosine angle between all the documents and the new document are calculated.  The angle closet to one represent similar documents, while those close to zero are not similar.  From this measure, we can infer if the tweet is relevant to fraud and have some measure of just “how” certain we are with this assumption.

## LDA

LDA assumes documents are produced from a mixture of topics. Those topics then generate words based on a Bayesian process with a Dirichlet probability distribution as its existing prior.  
LDA determines the mixture of topics in that document. For example, the document might contain 1/2 the topic “health” and 1/2 the topic “vegetables.”  Using each topic’s multinomial distribution, output words to fill the document’s word slots. In our example, the “health” topic is 1/2 our document, or 3 words. The “health” topic might have the word “diet” at 20% probability or “exercise” at 15%, so it will fill the document word slots based on those probabilities.  Given these assumptions of how documents are created, LDA backtracks and tries to figure out what topics would create those documents in the first place.

## LDA performed worse

Here the measured performance of LDA was substantially worse than LSA.  The reason for this is likely overfitting, something hard to avoid using a Bayesian process with so many inputs.  Remaining work needs to be preformed to pair down the parameters of the model.  As the LSI model produces reasonable results that pass a human inference test, this model was adopted as the basis of the machine learning used by the OAG.

Thanks for reading my readme!  Questions or comments can be handled through github or by contacting Max Feinglass at:

mfeinglass@gmail.com