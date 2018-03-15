# Yummly-Recipe-Recommendation-System
Yummly Similar Recipe And Similar Ingredient Recommender

Research Questions

The primary aim of the project is to develop a recommendation system to suggest similar recipes, similar ingredients and complementary ingredients to provide users the functionality of identifying substitutable ingredients and alternate recipes.
The research questions addressed in the project are as follows:
1.	What is the most important ingredient in a recipe?
2.	What is the alternative for a missing ingredient in a recipe? 
3.	Which ingredients complement one another? 
4.	Can the recommender system suggest similar recipes based on either recipe names or ingredients in a recipe? Which model performs better? 

Data 
The primary dataset is from the recipe aggregator website Yummly. The data contains 27,000+ recipes with an approximate size of 1.5 GB. It contains a collated set of recipes with each recipe in a separate JSON file. While the size of the dataset is relatively small, it provides rich and interesting features such as, nutritional information, images, list of ingredients, cuisines, and the course of the meal during which the recipe can be served. However, for the purpose of this project we limit the attributes to recipe names, ingredients and cuisines.

Additionally, we scraped the BBC Food ingredients website to extract around 1100 ingredient names to help standardize the Yummly recipes. Using the same website, we scraped and created a data set of related ingredients, which was used to suggest alternative ingredients or substitutions for a given ingredient in our recommendation system.
Related work
Teng, Lin and Adamic in the paper “Recipe recommendation using ingredient network” focus on providing recipe recommendations based on ingredients and text mining of user reviews. Allrecipes.com, is used as the primary dataset. Recipes were mainly classified as either savory or sweet. Within this classification, pairs of ingredients were identified based on pointwise mutual information (PMI) to create a ingredient complement network. The research methods included text mining on user reviews for recommendations using Latent Semantic Analysis.

Another paper titled, Kissing Cuisines, used 157k recipes from over 200 cuisines obtained through Yummly, BBC Food data and country health statistics. In this paper different cuisines were characterized based on ingredients and flavors. Notable ingredients were identified and Jensen-Shannon (JS) divergence method was applied to find the similarity among ingredients.     
Methods and Approach 
The BBC Food website was scraped using the BeautifulSoup package in Python. As the Yummly ingredient list contained measurements, cutting techniques and ingredient forms, the list of ingredients from BBC helped standardize the ingredient list. The Yummly ingredients were tokenized i.e  one word and two word combinations were obtained using n-grams and validated against the BBC Food data. We also used stemming for recipe names and ensured that all numbers, punctuations, and unnecessary words were ignored. We pluralized the ingredients found in BBC using the NLTK libraries to ensure matches between BBC data and our ingredient lists.

Research Question 1: Top Ingredient Recommendation Using TF-IDF
To identify the most important/unique ingredients in every recipe, we assigned TF-IDF scores to every ingredient using the Tfidf Model in the gensim library. A term frequency model down weighs the most commonly occurring ingredients and gives a higher weight to unique ones. The input to this model is a list of list for ingredients in every recipe. To analyze the results, a data frame was created to load the results for visualizations in Tableau with an interactive interface in Tableau public where any user can pick a recipe from the Yummly recipe corpus and visualize the top unique ingredients for every recipe.
Here is the link to the dashboard:
https://public.tableau.com/profile/sneha.vasanth#!/vizhome/YummlyTopUniqueIngredientIdenfier/TopIngredientsTF-IDF

Research Question 2 : Similar ingredient recommender based on context
To suggest alternative ingredients for substitution in a recipe, as a first pass, we check the related ingredient list obtained from the BBC Food website. If a related ingredient is not found, the second pass for the ingredient uses the Word2Vec model to suggest an alternative ingredient. For some ingredients, while no related ingredients were obtained from the BBC Food data, the Word2vec provided relevant alternatives. Further, the Word2Vec model suggested not just alternate ingredients, but complementary ingredients that occur in similar contexts.
To identify similar ingredients based on the recipe context they occur in, we passed the processed ingredient list of lists for every recipe to the gensim Word2Vec model. We chose a dimensionality of 300 and a window size of 1 for context. 
It was interesting to see that for an input like pasta, the similar ingredients were almost the same for both (penne, spaghetti,etc). For an ingredient like cumin, while no related ingredients were obtained from the BBC list, the Word2Vec model provided paprika, coriander and turmeric as alternatives. However, for an input like rice, BBC ingredients were much closer to the exact version of rice (E.g. Rice flour ) while the Word2Vec returned results like tofu, vermicelli, etc. which are similar in terms of the context they occur in but are not necessarily the same ingredient.  
Here is the link to the dashboard:
https://public.tableau.com/profile/sneha.vasanth#!/vizhome/YummlySimilarIngredientRecommender/SimilarIngredientsWord2VecPMI

Research Question 3 : Complementary Ingredients Using PMI
To learn how ingredients can be combined to enhance a recipe, we employed the occurrences of ingredients across our dataset to learn the user’s knowledge about combination of ingredients (Teng, Lin & Adamic, 2012). Pointwise mutual information gives the probability that two ingredients occur together against the probability that they occur separately. With duplicates eliminated, over 10,000 unique pairs of ingredients were created and provided to a user defined function to calculate the PMI scores. A high PMI score implies that the ingredients complement each other well and vice versa. Finally a Tableau visualization enables a user to identify the top 5 ingredients that complement a given input along with the bottom 5 ingredients that do not go well with the given input.

Here is the link to the dashboard:
https://public.tableau.com/profile/sneha.vasanth#!/vizhome/YummlySimilarIngredientRecommender/SimilarIngredientsWord2VecPMI

Research Question 4: Similar Recipe Recommendation Using TF-IDF and Jaccard Similarity

Similarity based on Recipe Ingredients : TF-IDF and Cosine Similarity
To recommend the most similar recipes, we passed the results of the TF-IDF model for every recipe and used the gensim library similarities function - MatrixSimilarity,  to get the cosine similarity between documents. We restricted the results of this function to get only the top 10 similar recipes. Among the top 10, we eliminated recipes with same names (i.e. TF-IDF score 1.0)
Similarity based on Recipe Name : Jaccard Similarity
Jaccard String Similarity is useful to calculate how close two recipe names are based on the words they share and don’t share. Given this simplistic model, we appended the cuisine name to each recipe to bias the similarity towards recipes from the same cuisine. For all the recipes in the dataset, we calculated the string similarity for each pair of recipes and stored the top 10 similar recommendations for each recipe (excluding itself) in a csv file. While the Jaccard Similarity based model is not as sophisticated as a TF-IDF model, the potential uses of this model are to find exact matches and duplicates of recipes, to serve as an autocomplete feature, and also return recommendations for user-input recipe names that do not exist in the dataset.
To analyze the results, we created a data frame to load the results for visualizations in Tableau and created an interactive interface in Tableau public where any user can pick a recipe from the Yummly recipe corpus in the input text and visualize the top 10 similar recipes for every recipe along with their similarity score. We compared with the similar recipes we obtained from Jaccard Similarity based on recipe names to understand the difference in recommendations for various recipes.
Here is the link to the dashboard: https://public.tableau.com/profile/sneha.vasanth#!/vizhome/YummlySimilarRecipeRecommender/SimilarRecipeRecommenderTF-IDFandJaccard

Model Evaluation: Recipe Recommender 
Since our approach was unsupervised, our metric for evaluation of accuracy was qualitative. In time, we hope to evaluate the ingredient based recommender models.
Ingredient Based Recipe Recommender
Of the 27000+ recipes that we had, we found that around 12000 recipes had similar recipes with 0.70 cosine similarity score for the TF-IDF model and hence we chose this as a threshold for the recipes in our test sample. Of this sample, we randomly chose 50 recipes and classified them manually by mapping them with the cleaned & standardized list of ingredients for the recipes. The recipes with 3 or more dissimilar ingredients were classified as not similar, else they were marked as similar. We found that the classification accuracy was 68% for this sample.
Name Based Recipe Recommender
We chose a jaccard similarity threshold of 0.7 because we saw that around  of the recipes in our dataset had some possible neighbour to recommend with this cutoff. We then randomly chose 60 recipes and manually classified the 240 recommendations that had a similarity greater than 0.7,  as correct or incorrect based on subjective judgement and inspection of recipe names, and ingredients. The classification accuracy was found to be 69.96% for this sample.

Results
We have the following data products and use cases as a result of the analysis.

Data Model:
1.	An Ingredient - Ingredient  and Recipe-Recipe similarity matrix.
2.	Cleaned ingredient list of 1000+ food items from the BBC website.
3.	Related Ingredient list for certain food items scraped and cleaned from the BBC website.

Functions:
4.	To identify the uniqueness and define the importance of an ingredient in a recipe.
5.	To suggest alternative ingredients /substitutions for a given ingredient.
6.	To suggests complementary ingredients that help one decide a meal combination.
7.	To suggest similar recipes based on a user input of Yummly or random recipe.

Visualizations
8.	Three Tableau Dashboards built on top of our above models for the end-user.
Limitations
1.	BBC food data that we used to standardize our list of recipes is not very comprehensive and does not account for regional names of ingredients (E.g. Hing is a colloquial term for Asafoetida). Due to this, during our data standardizing process, we lost out on these ingredients.
2.	During our initial assessment of data, we found that both BBC and Yummly datasets have ingredients that are 3 words or more. But for our models we only considered unigrams and bigrams.
3.	Yummly dataset is skewed in terms of cuisines, mainly American dishes. Due to this, the TF-IDF and PMI scores for ingredients might be biased for ingredients found in the American dishes.
4.	Our models are not very generalizable, however given more data in the form of recipes, cuisines and ingredients - we could apply the same analysis on the data and better our models.


Future work
In the future, we hope to combine the analysis into a helpful resource for cooking enthusiasts. The following steps would be beneficial.
1.	Using a scalable framework for data processing.
2.	Creating a Flask API for similar ingredients predictions and similar recipe predictions
3.	Conducting Network Analysis of ingredients and presenting insights on ingredient pairs.
4.	Making Cuisine-Appropriate Recommendations for ingredients and recipes for user inputs.
5.	Combining the name and ingredient based recipe recommender into a single model.


References
Sajadmanesh, Jafarzadeh, Osia, Rabiee, Haddadi, Mejova, M, Musolesi, E.D. Cristofaro, G.Stringhini. (2016). Kissing Cuisines: Exploring Worldwide Culinary Habits on the Web.
Teng, C., Lin, Y., & Adamic, L. (2012). Recipe recommendation using ingredient networks. Proceedings of the 4th Annual ACM Web Science Conference, 298-307.
