# Graph Analytics of Tech Stock Social Network

When researching on a stock/company, we often want to know who its competitors and business partners are, and who is the leader of its peers. Here, I developed a convenient way of doing this research by using graph analytics. 

For this purpose, I analyzed 100,974 news articles related to 633 stocks in the Technology Company list from NASDAQ's website, where the stocks were assigned into different industry groups. 

Table 1. Example of the stocks in the tech company list
![1 stocklist](https://user-images.githubusercontent.com/44976640/53704509-be880800-3de2-11e9-8ea0-871c79ebb72d.PNG)
                
Among these news articles, 72.5% of the articles are specific for one single stock, which can't be used to build the graph, and therefore, were excluded. 2.5% of the articles are related to five to tens of stocks. In many cases, these articles simply summarized the price changes of stocks that may not be closely related in business.  This kind of news was also excluded. Only news articles relating to two to four stocks were used to build stock network graph. This cohort consists of 27,229 news articles and 600 stocks. The data preprocessing was done using Python, and after filtering out the unwanted news, I got the following list that was used to build the graph.

Table 2. Example of the stocks and newsID in the dataset for building the graph

![2 final dataset](https://user-images.githubusercontent.com/44976640/53704538-ff801c80-3de2-11e9-8f0c-6f5131a6553a.PNG)

In this dataset, the relationship is bi-directional, therefore there are at least two records for each news ID, representing bi-directional relationship between each pair of stocks.  As you can see in Table 2,  NewsID "196748" connects two stocks, has two records, one direction each. NewsID "199630" connects 3 stocks, and has six records. We will discuss in more detail later. 

Since we have 27,229 news articles, it will be too many nodes for Neo4j to handle promptly, and the graph will be too busy to view if we load all news articles into Neo4j as nodes.  Instead, we can load only  stocks as nodes, each news article will be represented as the relationship(s) between stocks, each news article is stored as 1 count in the count property of the relationships. We can use the following cypher command to create the graph, and store the news article count as a relationship property.

![3 cypher 1](https://user-images.githubusercontent.com/44976640/53704553-29d1da00-3de3-11e9-8824-62e379673509.PNG)

Now we can use the following methods to detect the communities, as well as centrality and page rank of each stock node.
![4 graph algorythm](https://user-images.githubusercontent.com/44976640/53705369-99e35e80-3de9-11e9-8fc3-82a0295440bd.PNG)

After labeling the nodes by the industry of the stocks, and then colored respectively, we can now see the social network of the stocks in communities that have at least two members in each community with the following query.
![7 cypher qyery](https://user-images.githubusercontent.com/44976640/53704658-4884a080-3de4-11e9-9faa-fed06699942e.PNG)


The resulted social network of tech stocks is shown below. As expected, stocks in the same or similar industry tend to be in the same or nearby communities.  For example, professional service companies tend to connect with each other or with commercial service companies, software companies connect with electronic data processing companies (DATA) and computer manufacturers, and computer manufacturers connect with semiconductor companies.
![5 all_colorbyindustry](https://user-images.githubusercontent.com/44976640/53704645-1a06c580-3de4-11e9-8633-518af2b6b9da.png)


Sitting in the center of this social network are technology superstars, such as AAPL, MSFT, CSCO, IBM, etc. 

![6 center](https://user-images.githubusercontent.com/44976640/53704687-8e416900-3de4-11e9-9ab2-d06c0f126ec7.PNG)

Most of the stocks in the center belong to three big communities. They are semiconductor group (bottom right), software and electronic data processing group (middle), and computer and equipment group (top). Because of the business diversity of some companies, the single-tone classification in the original list may not describe the company/stock very well. For an example, IBM is classified as Computer Manufacturer. But we know IBM is also a big player in data processing and software. Community analysis assigned it in the software and data group, because of news articles mentioning IBM together with MSFT more often than with other computer and equipment manufacturers.

![8 top 3 community](https://user-images.githubusercontent.com/44976640/53704698-af09be80-3de4-11e9-9275-6471aa8d536c.png)

In order to better understand the stock network in terms of the performance, stocks in this network were also grouped into 10 clusters based on the performance since 03/17/2017 and colored by performance groups. 

![9 clustering 1](https://user-images.githubusercontent.com/44976640/53704710-c5b01580-3de4-11e9-94b1-3f64a10e5f13.PNG)

Cluster 9 (colored in red) are stocks with price below the starting point (03/17/2017) for most of the time, class 0 (colored in orange) are stocks perform similar as NASDAQ index. Clusters 3,5, and 7 (colored in light green) are stocks price went up to around 1.5x of the original prices. Clusters 2 and 4 (colored in aqua) are stocks that increased around 2x of the original prices. Clusters 1, 6, and 8 (colored in dark green) are stocks that rose between 3 to 6 times of the original prices. 

![9 clustering 2](https://user-images.githubusercontent.com/44976640/53704719-d496c800-3de4-11e9-9c8f-666d01fb5ab0.PNG)

![9 all_updown](https://user-images.githubusercontent.com/44976640/53704731-ed9f7900-3de4-11e9-81c2-8064b138b7cf.PNG)


When reporting a stock, news article will sometimes also mention its peers in the same business, as well as upstream and downstream partners. This gives us an opportunity to build stock networks and have a big picture of major players in the field.  With the graph built using the news data, we can easily find out players in the business field of interest. For example, on the top-center of the network, there is a group of 5 video game stocks (EA, ATVI, GLUU, ZNGA, TTWO). The data shows that on overall, video game stocks are good performers.
On the upper right, there is a group of 6 solar energy-related companies (SEDG, SOL, CSIQ, SPWR, JKS, ENPH). Four of them perform similar to the NASDAQ index, while two of them (SEDG, ENPH) outperformed the market.

![10 detail ver2](https://user-images.githubusercontent.com/44976640/53704745-014adf80-3de5-11e9-891c-2e4a50d3c6fc.PNG)

### Discussion:
Neo4j can only create relationship with one direction. But in our case, the relationship is bi-directional. In order to avoid creating an extra unnecessary relationship just to represent the different directions of the relationship, we created relationship in one direction only between two stock nodes, but we can query the graph database without directions to overcome this inconvenience.







