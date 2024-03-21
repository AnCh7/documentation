### Kolmogorov-Smirnov

**ELI5:** Imagine you have two groups of people, each measuring their heights. The Kolmogorov-Smirnov (K-S) test is like a tool that helps you figure out  if the two groups have similar height patterns or if they are different. It's like comparing two lines of people sorted by height to see if they form similar patterns or not. If the lines look pretty much the same  (heights are distributed similarly), the K-S test tells you that the  groups are likely similar. But if the patterns of height are quite  different in the two lines, the K-S test will notice this and tell you  that the groups are probably different in their height distributions.

### Fisher’s Exact Test

**ELI5:** Imagine you have a small bag of marbles, some red and some blue. Now, imagine you have two boxes, and you put some of these marbles into each box randomly. Fisher's Exact Test is like a tool that helps you figure out if the way the red and blue marbles are spread between the two boxes is just a random chance or if there's a specific pattern. So, if you find more red marbles in one box and more blue in the other, Fisher's Exact Test can tell you if this happened by luck or if there's something special about how the marbles were distributed. This is super useful in science and research when you want to know if the results you see (like how many people get better with a new medicine versus an old one) are just a coincidence or if there's a real difference.

### Isolation Forest

**ELI5:** Imagine you're in a forest where each tree is a detective trying to find the "strangest" trees (which are like unusual data points in your  data). These detective trees do this by asking random questions like "Is this tree shorter than others?" or "Does this tree have a different  type of leaf?". The weird trees get found out quickly because they're  different from the rest. In an Isolation Forest, the easier it is to  find a tree that stands out by asking these random questions, the more  likely it is to be unusual. This is how the algorithm spots things that  don't quite fit in - the anomalies in your data.

Isolation forests are tree based models specifically used for outlier detection. The IF isolates observations by randomly selecting a feature and then  randomly selecting a split value between the maximum and minimum values  of the selected feature. The number of splittings required to isolate a  sample is equivalent to the path length from the root node to the  terminating node. This path length, averaged over a forest of random trees, is a measure of normality and is used to define an anomaly score. Outliers can typically be isolated quicker, leading to shorter  paths. The algorithm is suitable for low to medium dimensional tabular  data.

### Thresholding with false discovery rate

> https://matthew-brett.github.io/teaching/fdr.html

The false discovery rate is a different *type* of correction than family-wise correction. Instead of controlling for the risk of *any tests* falsely being declared significant under the null hypothesis, FDR will control the *number of tests falsely declared significant as a proportion of the number of all tests declared significant*.
