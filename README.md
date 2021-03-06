# BA_Assignment
**LDA TOPIC model for Amazon product review "Patio, Lawn and Garden":**

The following document consistes of an LDA topic model for the reviews of the Amazon product "Patio,Lawn and Garden".

**Data:**

There are a total of 151,254 reviews. We analyze the Text of the Review in this particular model.

**Cleaning corpus:**

We remove punctuation and some common and some irrelevant stop words to get a fairly clean data set.

```
stop_words <- stopwords("SMART")
## additional junk words showing up in the data
stop_words <- c(stop_words, "said", "the", "also", "say", "just", "like","for",
                "us", "can", "may", "now", "year", "according", "mr")
stop_words <- tolower(stop_words)
news_2015 <- gsub("'", "", news_2015) # remove apostrophes
news_2015 <- gsub("[[:punct:]]", " ", news_2015)  # replace punctuation with space
news_2015 <- gsub("[[:cntrl:]]", " ", news_2015)  # replace control characters with space
news_2015 <- gsub("^[[:space:]]+", "", news_2015) # remove whitespace at beginning of documents
news_2015 <- gsub("[[:space:]]+$", "", news_2015) # remove whitespace at end of documents
news_2015 <- gsub("[^a-zA-Z -]", " ", news_2015) # allows only letters
news_2015 <- tolower(news_2015)  # force to lowercase

## get rid of blank docs
news_2015 <- news_2015[news_2015 != ""]

# tokenize on space and output as a list:
doc.list <- strsplit(news_2015, "[[:space:]]+")

# compute the table of terms:
term.table <- table(unlist(doc.list))
term.table <- sort(term.table, decreasing = TRUE)

# remove terms that are stop words or occur fewer than 5 times:
del <- names(term.table) %in% stop_words | term.table < 5
term.table <- term.table[!del]
term.table <- term.table[names(term.table) != ""]
vocab <- names(term.table)

# now put the documents into the format required by the lda package:
get.terms <- function(x) {
        index <- match(x, vocab)
        index <- index[!is.na(index)]
        rbind(as.integer(index - 1), as.integer(rep(1, length(index))))
}
documents <- lapply(doc.list, get.terms)
```
**Compute some statistics related to the data set**
D <- length(documents)  # number of documents (107)

W <- length(vocab)  # number of terms in the vocab (1911)

```
doc.length <- sapply(documents, function(x) sum(x[2, ]))  # number of tokens per document [312, 288, 170, 436, 291, ...]
N <- sum(doc.length)  # total number of tokens in the data (56196)
term.frequency <- as.integer(term.table)
```
**MCMC and model tuning parameters:**

K <- 10

G <- 3000

alpha <- 0.02

eta <- 0.02

**Fit the model:**
```
library(lda)
set.seed(357)
t1 <- Sys.time()
fit <- lda.collapsed.gibbs.sampler(documents = documents, K = K, vocab = vocab,
                                   num.iterations = G, alpha = alpha,
                                   eta = eta, initial = NULL, burnin = 0,
                                   compute.log.likelihood = TRUE)
t2 <- Sys.time()
```
**Display runtime:**

The Time difference was 12.39 mins

```
t2 - t1
theta <- t(apply(fit$document_sums + alpha, 2, function(x) x/sum(x)))
phi <- t(apply(t(fit$topics) + eta, 2, function(x) x/sum(x)))

news_for_LDA <- list(phi = phi,
                     theta = theta,
                     doc.length = doc.length,
                     vocab = vocab,
                     term.frequency = term.frequency)

library(LDAvis)
library(servr)
```

**Create the JSON object to feed the visualization:**
```
json <- createJSON(phi = news_for_LDA$phi,
                   theta = news_for_LDA$theta,
                   doc.length = news_for_LDA$doc.length,
                   vocab = news_for_LDA$vocab,
                   term.frequency = news_for_LDA$term.frequency)
serVis(json, out.dir = 'viz', open.browser = TRUE)
```

**Result:**

It might be interesting to note that the Intertopic Distance between Topic 5 and Topic 7 with respect to the term market is non-existent whereas Topics 3&2 and 8&10 are correlated to a certain extent. 

Click below to view the LDA Topic Modeling Visualization

[Click Here] (https://cdn.rawgit.com/AshwinRajendran/BA_Assignment/master/index.html)


