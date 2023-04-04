# translated-challenge
Exercise for translated challenge
## Exercise 1
See [exercise1](Challenge.ipynb)

## Exercise 2
``` 
This exercise is about SQL and data management. You have to answer questions by commenting on your answers.
Suppose that you work for a company whose main business is selling products on an e-commerce website.
You have the following tables organized in a database:
customers
    id
    firstname
    lastname
    data_of_birth
    country
orders
    id
    id_customer
    order_cost
orders_items
    id
    id_order
    id_product
    quantity
products
    id
    name
    price
Questions:
1- Can you explain the purpose of the orders_items table?
2- Can you write a SQL query to find the average order cost per country?
3- Can you write a SQL query to find the name of the highest price product sold to an Italian customer?
4- How could you optimize the orders table assuming you have to manage a lot of queries?
5- What would you change if the amount of data is too big to run these queries? (suppose to have 500TB of data)
```
### 1 Can you explain the purpose of the orders_items table?
The orders_items table is a bridge table between orders and products. It contains the quantity of each product in each order. It is necessary to have this table because a product can be sold in different quantities in different orders. Referring to ER diagram, it is a many-to-many relationship between orders and products.


### 2 Can you write a SQL query to find the average order cost per country?
```sql
SELECT country, AVG(order_cost) 
FROM customers
JOIN orders ON customers.id = orders.id_customer
GROUP BY country
```

### 3 Can you write a SQL query to find the name of the highest price product sold to an Italian customer?
```sql
SELECT products.name
FROM products
JOIN orders_items ON products.id = orders_items.id_product
JOIN orders ON orders_items.id_order = orders.id
JOIN customers ON orders.id_customer = customers.id
WHERE customers.country = 'Italy' 
ORDER BY products.price DESC
LIMIT 1
```

### 4 How could you optimize the orders table assuming you have to manage a lot of queries?
In order to optimize the orders table we can add an index over the id_custumer column. We will retrieve the orders of a customer faster.
We can also add an index on the order_cost column to optimize the query that filters on this column. However, only one index can be a primary index (which is more efficient than a secondary index).

Another solution could be to add columns of the other tables that are joined in the query. For example, if we add the country column in the orders table, we will not need to join the customers table in the query that filters on the country column. This solution is not optimal because we will have to update the orders table when we update the customers table. But it is a solution to optimize the query.

### 5 What would you change if the amount of data is too big to run these queries? (suppose to have 500TB of data)
Is the amount of data is too big to run the queries, we can use a distribuited database or a NoSQL database, optimizing these database to run the queries. We should choosing the fragmentation technique that is the most appropriate for the queries we want to run. We can also use a cache to store the results of the queries that are run often. This will reduce the number of queries to the database. 
A possible NoSQL database depends to the queries that are tipically done. For example, if we have a lot of queries involving the custumer table and the orders of a custumer, we can use a document databases, where the custumer table is a document and the orders of a custumer are stored in a list in the document. If we have a lot of queries involving the orders table, we can use a graph database, where the orders table is a node and the products table is a node. The orders_items table is a relationship between the orders and the products nodes. 

## Exercise 3
### 1 How would you design a data pipeline for a Machine Translation system? (e.g. necessary steps, main challenges, etc.)
I assume that the data are cleaned in a certain way. For example, are already divided in pair of sentences (source and target language).
1- We need to tokenize the sentences. We can use a tokenizer that is already trained on a large corpus of sentences. For example, we can use the tokenizer of the BERT model. 
2- We need to encode the sentences. We can use a pre-trained model that is already trained on a large corpus of sentences. (encoder-decoder transformer model, that is thought to translate sentences)
3- Decode the sentence
4- Detokenize the sentence
5- Evaluate the translation. We can use a metric like BLUE.

If we want to train a new model we can use a encoder-decoder architecture and train it on a large corpus of sentences in a semi-supervised way. We can also use a pre-trained model for the encoder and train the decoder. The dataset shoudld be the same text in different languages. For example, the EU institutions publish their documents in different languages.

### 2 What would you do to collect new and good quality data from the web? Assume you want to use them to train a neural model for Machine Translation.
As before we can retrieve the data from institution that publish their documents in different languages. Maybe we can use a web crawler to retrieve text data from the web. Then we need to clean the data. We can use a language detector to select documents that use more than one languages, and use a already trained model to check automatically if the document contains the same text in different language. Then we can use this data to train a better model. 

### 3 Suppose that low quality translations created by the system are post-edited by professional translators. How would you use this process to monitor the quality of the Machine Translation system?
Immagine to have a dataset with rows of tuple (sentence_in_source_lang, sentence_in_target_lang_from_ML, sentence_in_target_lang_corrected). Now we can use the same metrics used to evaluate the system between the sentence_in_target_lang_corrected and the sentence_in_source_lang. So, we will have an idea of the level of the translation system compared to the professional translator. We can also use this data to finetuning this model, or implementing a RL algorithm to improve the translation system.
