# BA_Assignment
**LDA TOPIC model for Amazon product review "Patio, Lawn and Garden"**
The following document consistes of an LDA topic model for the reviews of the Amazon product "Patio,Lawn and Garden".

**DATA**
There are a total of 151,254 reviews. We analyze the Text of the Review in this particular model.

**Cleaning corpus**
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


```
https://cdn.rawgit.com/AshwinRajendran/BA_Assignment/master/index.html
```
