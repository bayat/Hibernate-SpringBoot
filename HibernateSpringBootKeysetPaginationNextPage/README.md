**[How To Implement Keyset Pagination With "Next Page" Button In Spring Boot](https://github.com/AnghelLeonard/Hibernate-SpringBoot/tree/master/HibernateSpringBootKeysetPaginationNextPage)**

**Description:** When we rely on an *offset* paging we have the performance penalty induced by throwing away *n* records before reached the desired *offset*. Larger *n* leads to a significant performance penalty. When we have a large *n* is better to rely on *keyset* pagination which maintain a "constant" time for large datasets. In order to understand how bad *offset* can perform please check this [article](http://allyouneedisbackend.com/blog/2017/09/24/the-sql-i-love-part-1-scanning-large-table/):

Screenshot from that article (*offset* pagination):
![](https://github.com/AnghelLeonard/Hibernate-SpringBoot/blob/master/HibernateSpringBootKeysetPagination/offset%20pagination.png)

**Need to know if there are more records?**\
By its nature, *keyset* doesn't use a `SELECT COUNT` to fetch the number of total records. But, we a little tweak we can easily say if there are more records, therefore to show a button of type `Next Page`:

`public AuthorView fetchNextPage(long id, int limit) {`\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`List<Author> authors = authorRepository.fetchAll(id, limit + 1);`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`if (authors.size() == (limit + 1)) {`\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`authors.remove(authors.size() - 1);`\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`return new AuthorView(authors, true);`\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`}`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`return new AuthorView(authors, false);`\
`}`

Or, like this (rely on `Author.toString()` method):

`public Map<List<Author>, Boolean> fetchNextPage(long id, int limit) {`\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`List<Author> authors = authorRepository.fetchAll(id, limit + 1);`
        
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`if(authors.size() == (limit + 1)) {`\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`authors.remove(authors.size() -1);`\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`return Collections.singletonMap(authors, true);`\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`}`
        
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`return Collections.singletonMap(authors, false);`\
`}`

A `Previous Page` button can be implemented easily based on the first record.

**Key points:**\
     - choose the column(s) to act as the latest visited record (e.g., `id`)\
     - use the column(s) in the `WHERE` and `ORDER BY` clauses of your SQL

**Sample:**\
![](https://github.com/AnghelLeonard/Hibernate-SpringBoot/blob/master/HibernateSpringBootKeysetPaginationNextPage/keyset%20pagination%20with%20next%20page%20button.png)

-------------------------------

**You may like to try as well:**
<a href="https://leanpub.com/java-persistence-performance-illustrated-guide"><p align="center"><img src="https://github.com/AnghelLeonard/Hibernate-SpringBoot/blob/master/Java%20Persistence%20Performance%20Illustrated%20Guide.jpg" height="410" width="350"/></p></a>

