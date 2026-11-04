# DC Assignment 2: Design a Logical Model and Advanced SQL

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

#### Submission Parameters:
* Submission Due Date: `April 07, 2026`
* Weight: 70% of total grade
* The branch name for your repo should be: `assignment-two`
* What to submit for this assignment:
    * This markdown (Assignment2.md) with written responses in Section 1 and 4
    * Two Entity-Relationship Diagrams (preferably in a pdf, jpeg, png format).
    * One .sql file 
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sql/pulls/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [ ] 🚨🚨🚨 After forking, open the **Actions** tab in your own SQL repository. If prompted, click **I understand my workflows, go ahead and enable them** before opening your assignment pull request. This enables automatic SQL feedback on your pull request.
- [ ] Create a branch called `assignment-two`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via our Slack. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.

***

## Section 1:
You can start this section following *session 1*, but you may want to wait until you feel comfortable wtih basic SQL query writing. 

Steps to complete this part of the assignment:
- Design a logical data model
- Duplicate the logical data model and add another table to it following the instructions
- Write, within this markdown file, an answer to Prompt 3


###  Design a Logical Model

#### Prompt 1
Design a logical model for a small bookstore. 📚

At the minimum it should have employee, order, sales, customer, and book entities (tables). Determine sensible column and table design based on what you know about these concepts. Keep it simple, but work out sensible relationships to keep tables reasonably sized. 

Additionally, include a date table. 
A date table (also called a calendar table) is a permanent table containing a list of dates and various components of those dates. Some theory, tips, and commentary can be found [here](https://www.sqlshack.com/designing-a-calendar-table/), [here](https://www.mssqltips.com/sqlservertip/4054/creating-a-date-dimension-or-calendar-table-in-sql-server/) and [here](https://sqlgeekspro.com/creating-calendar-table-sql-server/). 
Remember, you don't actually need to run any of the queries in these articles, but instead understand *why* date tables in SQL make sense, and how to situate them within your logical models.

There are several tools online you can use, I'd recommend [Draw.io](https://www.drawio.com/) or [LucidChart](https://www.lucidchart.com/pages/).

**HINT:** You do not need to create any data for this prompt. This is a conceptual model only. 

#### Prompt 2
We want to create employee shifts, splitting up the day into morning and evening. Add this to the ERD.

#### Prompt 3
The store wants to keep customer addresses. Propose two architectures for the CUSTOMER_ADDRESS table, one that will retain changes, and another that will overwrite. Which is type 1, which is type 2? 

**HINT:** search type 1 vs type 2 slowly changing dimensions. 

```
Type 1 SCD: Overwriting Customer Addresses

The first architecture uses a Type 1 Slowly Changing Dimension (SCD), in which a customer's previous address is overwritten whenever their address changes.

The CUSTOMER_ADDRESS table would contain customer_id (both primary key and foreign key), street_address, city, province, and postal_code. Each customer would have one address record, which is updated whenever they move.

This approach is relatively simple and efficient because it only stores the customer's current address. Yet, it does not preserve historical information, meaning previous addresses cannot be retrieved from this table.

Type 2 SCD: Retaining Customer Address History

The second architecture uses a Type 2 Slowly Changing Dimension, which preserves historical address changes by creating a new record whenever a customer updates their address.

The CUSTOMER_ADDRESS table would include address_id (primary key), customer_id (foreign key), street_address, city, province, postal_code, valid_from, valid_to, and is_current.

For instance, if a customer moves from Toronto to Vancouver, their Toronto address would remain in the database with an ending validity date, while a new record would be created for their Vancouver address. The new record would be marked as current.

This architecture allows the bookstore to track historical changes and conduct analyses over time. However, it requires more storage and more complex queries. Retaining previous addresses also introduces additional privacy and data-retention considerations.

Comparison

Type 1 is more appropriate when the bookstore only needs a customer's current address, such as for deliveries. While Type 2 is useful when historical address information is necessary for auditing or legitimate business analysis. The choice depends on the bookstore's operational requirements and how long it needs to retain customer information.
```

***

## Section 2:
You can start this section following *session 4*.

Steps to complete this part of the assignment:
- Open the assignment2.sql file in DB Browser for SQLite:
	- from [Github](./02_activities/assignments/assignment2.sql)
	- or, from your local forked repository  
- Complete each question, by writing responses between the QUERY # and END QUERY blocks
- Some questions build on your earlier queries or tables. Keep track of the column names you use so you can reference them correctly in later questions.


### Write SQL

#### COALESCE
1. Our favourite manager wants a detailed long list of products, but is afraid of tables! We tell them, no problem! We can produce a list with all of the appropriate details. 

Using the following syntax you create our super cool and not at all needy manager a list:
```
SELECT 
product_name || ', ' || product_size|| ' (' || product_qty_type || ')'
FROM product
```

But wait! The product table has some bad data (a few NULL values). 
Find the NULLs and then using COALESCE, replace the NULL with a blank for the first column with nulls, and 'unit' for the second column with nulls. 

**HINT**: keep the syntax the same, but edited the correct components with the string. The `||` values concatenate the columns into strings. Edit the appropriate columns -- you're making two edits -- and the NULL rows will be fixed. All the other rows will remain the same.

<div align="center">-</div>

#### Windowed Functions
1. Write a query that selects from the customer_purchases table and numbers each customer’s visits to the farmer’s market (labeling each market date with a different number). Each customer’s first visit is labeled 1, second visit is labeled 2, etc. 

You can either display all rows in the customer_purchases table, with the counter changing on each new market date for each customer, or select only the unique market dates per customer (without purchase details) and number those visits. 

**HINT**: One of these approaches uses ROW_NUMBER() and one uses DENSE_RANK().

Filter the visits to dates before April 29, 2022.

2. Reverse the numbering of the query so each customer’s most recent visit is labeled 1, then write another query that uses this one as a subquery (or temp table) and filters the results to only the customer’s most recent visit. 
**HINT**: Do not use the previous visit dates filter.

3. Using a COUNT() window function, include a value along with each row of the customer_purchases table that indicates how many different times that customer has purchased that product_id.

You can make this a running count by including an ORDER BY within the PARTITION BY if desired.
Filter the visits to dates before April 29, 2022.

<div align="center">-</div>

#### String manipulations
1. Some product names in the product table have descriptions like "Jar" or "Organic". These are separated from the product name with a hyphen. Create a column using SUBSTR (and a couple of other commands) that captures these, but is otherwise NULL. Remove any trailing or leading whitespaces. Don't just use a case statement for each product! 

| product_name               | description |
|----------------------------|-------------|
| Habanero Peppers - Organic | Organic     |

**HINT**: you might need to use INSTR(product_name,'-') to find the hyphens. INSTR will help split the column. 

2. Filter the query to show any product_size value that contain a number with REGEXP. 

<div align="center">-</div>

#### UNION
1. Using a UNION, write a query that displays the market dates with the highest and lowest total sales.

**HINT**: There are a possibly a few ways to do this query, but if you're struggling, try the following: 1) Create a CTE/Temp Table to find sales values grouped dates; 2) Create another CTE/Temp table with a rank windowed function on the previous query to create "best day" and "worst day"; 3) Query the second temp table twice, once for the best day, once for the worst day, with a UNION binding them. 

***

## Section 3:
You can start this section following *session 5*.

Steps to complete this part of the assignment:
- Open the assignment2.sql file in DB Browser for SQLite:
	- from [Github](./02_activities/assignments/assignment2.sql)
	- or, from your local forked repository  
- Complete each question, by writing responses between the QUERY # and END QUERY blocks

### Write SQL

#### Cross Join
1. Suppose every vendor in the `vendor_inventory` table had 5 of each of their products to sell to **every** customer on record. How much money would each vendor make per product? Show this by vendor_name and product name, rather than using the IDs.

**HINT**: Be sure you select only relevant columns and rows. Remember, CROSS JOIN will explode your table rows, so CROSS JOIN should likely be a subquery. Think a bit about the row counts: how many distinct vendors, product names are there (x)? How many customers are there (y). Before your final group by you should have the product of those two queries (x\*y). 

<div align="center">-</div>

#### INSERT
1. Create a new table "product_units". This table will contain only products where the `product_qty_type = 'unit'`. It should use all of the columns from the product table, as well as a new column for the `CURRENT_TIMESTAMP`.  Name the timestamp column `snapshot_timestamp`.

2. Using `INSERT`, add a new row to the product_unit table (with an updated timestamp). This can be any product you desire (e.g. add another record for Apple Pie). 

<div align="center">-</div>

#### DELETE 
1. Delete the older record for whatever product you added.

**HINT**: If you don't specify a WHERE clause, [you are going to have a bad time](https://imgflip.com/i/8iq872).

<div align="center">-</div>

#### UPDATE
1. We want to add the current_quantity to the product_units table. First, add a new column, `current_quantity` to the table using the following syntax.
```
ALTER TABLE product_units
ADD current_quantity INT;
```

Then, using `UPDATE`, change the current_quantity equal to the **last** `quantity` value from the vendor_inventory details. 

**HINT**: This one is pretty hard. First, determine how to get the "last" quantity per product. Second, coalesce null values to 0 (if you don't have null values, figure out how to rearrange your query so you do.) Third, `SET current_quantity = (...your select statement...)`, remembering that WHERE can only accommodate one column. Finally, make sure you have a WHERE statement to update the right row, you'll need to use `product_units.product_id` to refer to the correct row within the product_units table. When you have all of these components, you can run the update statement.
*** 

## Section 4:
You can start this section anytime.

Steps to complete this part of the assignment:
- Read the article
- Write, within this markdown file, between 250 and 1000 words. No additional citations/sources are required.

### Ethics

Read: Boykis, V. (2019, October 16). _Neural nets are just people all the way down._ Normcore Tech. <br>
    https://vicki.substack.com/p/neural-nets-are-just-people-all-the

**What are some of the ethical issues important to this story?**

Consider, for example, concepts of labour, bias, LLM proliferation, moderating content, intersection of technology and society, ect. 



Vicki Boykis's (2019) article, Neural nets are just people all the way down, challenges the widespread assumption that artificial intelligence (AI) and machine learning operate as autonomous, objective technological systems. By tracing the human labour and decision-making processes underlying machine learning datasets, Boykis demonstrates that seemingly automated technologies are fundamentally dependent on human contributions. What I find particularly compelling about this argument is how it exposes the social and political dimensions of technologies often presented as neutral or independent of human intervention. The article raises several interconnected ethical concerns, particularly regarding invisible labour, algorithmic bias, the politics of classification, and accountability in AI development.

One of the most significant ethical issues concerns the invisibility and undervaluation of human labour. Boykis begins with an analogy between garment manufacturing and machine learning, highlighting how supposedly automated processes continue to depend on extensive manual work. Her discussion of ImageNet illustrates how large-scale machine learning datasets rely on workers who manually label and categorize millions of images, including those recruited through Amazon Mechanical Turk. Although these datasets have enabled major advances in computer vision, the individuals responsible for producing them often receive little recognition or financial compensation relative to the economic and technological value their work generates. This raises questions about the unequal distribution of benefits within the AI industry. While technological innovation is frequently attributed to researchers, corporations, and sophisticated algorithms, the contributions of data annotators and other workers remain largely invisible. From an ethical perspective, recognizing this labour also requires considering fair compensation, working conditions, and workers' participation in decisions about the technologies they help produce.

A second important concern involves the relationship between human classification and algorithmic bias. Boykis traces the development of ImageNet through earlier linguistic resources, including WordNet and the Brown Corpus, showing how contemporary machine learning systems inherit categories and classifications established through historical human decisions. This suggests that datasets are not simply neutral collections of information. Rather, they reflect particular cultural assumptions, institutional priorities, and ways of organizing knowledge. Decisions about what constitutes a category, which examples represent it, and how ambiguous cases should be classified inevitably involve interpretation. Consequently, biases embedded in these decisions can become reproduced and potentially amplified through machine learning systems.

Boykis' discussion of ImageNet Roulette is a particularly revealing example. The project exposed how image recognition systems could assign inappropriate, offensive, or socially loaded labels to photographs of people. This demonstrates that algorithmic bias cannot be understood solely as a technical problem involving inaccurate predictions. Moreover, it concerns the social consequences of translating historically situated classifications into apparently objective computational judgments. Such classifications become especially troubling when deployed in contexts involving identity, surveillance, or access to institutional resources. The ethical question is therefore not only whether an algorithm produces accurate results, but also whose perspectives inform its categories, whose identities are misrepresented, and who bears the consequences of those decisions.

These concerns have become increasingly relevant with the proliferation of large language models (LLMs) and generative AI. Although Boykis' article predates the contemporary expansion of these technologies, its central argument remains applicable. LLMs depend on extensive human-produced textual data, as well as human involvement in evaluating outputs and shaping model behaviour. Content moderation further complicates the idea of autonomous AI because judgments about harmful, inappropriate, or acceptable content are socially and culturally situated. What counts as offensive or legitimate expression may differ across linguistic, cultural, and political contexts. Consequently, moderation systems risk reproducing dominant norms while marginalizing perspectives that do not fit established classifications. This makes transparency and meaningful human oversight particularly important.

Finally, the article raises broader questions about responsibility and accountability. If machine learning systems are built upon layers of human decisions, organizations developing and deploying these technologies cannot attribute harmful outcomes solely to algorithms or imperfect datasets. Ethical responsibility extends across the entire technological process, including data collection, classification, model development, deployment, and ongoing evaluation. Addressing these concerns requires more than correcting individual biases after they become visible. It involves critically examining the institutional structures and power relations that determine whose knowledge, labour, and experiences are incorporated into technological systems.

For these reasons, Boykis's article encourages us to reconsider AI not as an independent technological force, but as a sociotechnical system shaped by human practices and social inequalities. In my view, the article's most important contribution is the recognition that technological advancement does not eliminate human responsibility. Instead, the increasing complexity and apparent autonomy of AI systems make it even more necessary and pressing to examine the people, institutions, and decisions that control and sustain them. Thus, a more ethical approach to AI must prioritize labour recognition, inclusive and critically examined data practices, and accountability for the social consequences of automated systems.

