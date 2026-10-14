We are trying to search, RAG is basically searching for 'x' out of 'y' where x is our query and y is our corpus
x and y can be text, audio, video, documents e.t.c

To search for 'x' we need to have y (from collection of what are we searching from)

To have y prepared for search we have this steps

### preprocessing
- Fetching data

- Spliting the fetched data into chunks (might not be needed if 'y' is so small but preferred)
    - How to split is as important as the below steps as the degree of split can affect the degree of search correctness
    - Some common split ways
        - Fixed size spliting
        - Text structure based (recommended is Recursive spliting)
        - Document structure-based spiliting 
    - read more https://docs.langchain.com/oss/python/integrations/splitters

- Decide which kind of search you want to perform
    - Lexical search
        - relying solely on exact matches of keywords - keywords based search
        - for lexical search some of the algorithm used are
            - TF-IDF
            - BM25 aka improved TF-IDF - industry baseline
    - Semantic search
        > The idea behind semantic search is to embed all entries in your corpus, whether they be sentences, paragraphs, or documents, into a vector space. At search time, the query is embedded into the same vector space and the closest embeddings from your corpus are found. These entries should have a high semantic similarity with the query.
        - aims to understand the meaning and context of the query and the documents being searched. This allows for more relevant and accurate search results, even when the exact keywords may not match.
        - for semantic search some search retrival algorithm used are
            - cosine similarities
            - ANN based algorithms such as tree-based algo e.g Annoy OR graph based algo e.g HNSW
        
    - For Semantic Search
        - We embed all entries in the corpus, chunks by chuncks into a vector space (basically convert 'media' into array of numbers whose numbers also carries semantic meaning)
        - these embeddings are dense representations of the entries
    
    - For Lexical Search
        - Historical to perform BM25 we do not need to embed the corpus nor query we perform so algorithm and this results to an entry in our corpus thats similar to the query using Lexical (exact word matching)
        - Modern approach can also perform lexical search by embedding the corpus and query into vector space (this embedding are called sparse representation)

        

### Search Time (Retreival stage)
- For Semantic Search
    - embed the query into same vector space (using same embedding model used in the preprocessing of the corpus)
    - The new embeding of the query can now be compared with corpus using the different algorithms from above
        - cosine similarities (numpy, tensorflow e.t.c has cosine function to call)
        - ANN based
            - Index the corpus embeddings
            - search the query embedding in the index (Annoy, HNSW, elastic_search also supports dense embedding uses HNSW)
    - the closest embeddings from your corpus should have a high semantic similarity with the query

- For Lexical Search
    - Modern follow
        - embed the query using sparse encoder models such as `naver/splade-cocondenser-ensembledistil`
        - run similarity using search retrival algorithm
    - History
        - Using elastic search, save the corpus in it without embedding, run search with query it uses bm25
        - use python rank_bm25  https://pypi.org/project/rank-bm25/

- Hybrid Retrieval: Combining Semantic and Keyword Search

