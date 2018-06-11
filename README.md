# MachineLearning on foreign exchange trading.
A MT5 trading agent (knn02.mq5) integrated with Machine Learning script in python (kmknn01.py).
I used the last 80 minute foreign exchange price ticks to predict the next 20 minutes.  
First, preprocess the price tick with differcing:  ((high-open)*bp,(open-low)*bp,(close-open)*bp,((high-open)-(open-low))*bp )

Then, I use K-Mean clustering to find 10 clusters for each mini-batch data (10000 samples).  So similar shape of the price change in the last 80 minutes, including grouping the shape similarity due to time shift.

Then, for each cluster, I averaging the price difference of each cluster sample as the next price change (up or down) of this cluster.  And then take the highest price high as the price high of the next 20 minute.  Similarly for the lowest price low.

Then, I stored all the centroid of each cluster from all the mini-batches, and then form a KNN model based on the centroid list.

To predict the next 20 minute price change, I feed in the last 80 minute price tick differencing into the KNN model, and get the nearest centroid point.  And then use the price change (up/down) and price high/low as the prediction at present.  The model is based on the belief that the future price change will repeat the historical price change pattern.

I use the MT5 platform to build the auto-trading agent.  The trading agent will write the last 80 price tick to a file, and the KNN python script will periodically scan the folder to read the price tick, and then write the prediction to the response time.  The trading agent will read the response file and make the trading decision.  If the price change is more for up and the price high change is greate than price low change, then it regards that as the buying signal, and place a buy position with the largest price change as the take-prodit and stop-loss point.  Similarly, if the price change is more for down, and the price low change is greater than the price high change, then it regards that as the selling signal, and place a sell position with the largest price change as the take-prodit and stop-loss point.

In the test simulation on the 6-month data, it approximately get a 2%-3% profit, and around 5% max drawback.
