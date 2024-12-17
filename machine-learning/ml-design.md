# Ads Predictions

## Architecture

Advertiser flow

1. Query-based targeting, shows ads based on query term
2. User-based targeting, subject to the specific user, region, demographic
3. Interest-based targeting, such as sports
4. Set-based targeting, people who have spent x number of time on website

## Auction

For every ad request, an auction takes place to determine which ads to show. The top relevant ads selected by the ad prediction system are given as an input to auction. Auction then looks at total value based on an ad's bid and relevance score.

Total value = bid + user engagement + ad quality + budget

- Bid = bid advertiser places for ad
- User engagement rate = estimate of engagement with ad
- Ad quality score = assessment of quality of ad by taking into account feedback
- Budget = advertiser budget for ad

Pacing an ad means spending ad budget evenly over a time period

## Features

1. Ad specific features like create time, content, ad negative engagement rate
2. Advertiser specific features like region-wise engagement, historical engagement rate
3. User-specific features like age, gender, interest
4. Context-specifc features like region, previous queries
5. User-ad features like embedding similarity
6. User-advertiser features like embedding similiarty

## Ad selection

1. Quick selection of ads for given query and user context

- in memory index to store ads, search query

1. Narrow down selection to top relevant ads

- use filter based on bid and predicted score (such as prior CPE)

1. Ranking based on simplistic model

- ML model to get ~ top x ads

---

# Entity Matching

Example A - name, city, age

Example B - name, city, age

- Attribute Embedding

Takes in two attributes and creates a word embedding (name -> embedding).

Options: glove, fasttext

- Attribute Similarity

Takes in attribute embeddings and encodes into representation that captures similarities and differences

Summarization can be: attention, hybrid, rnn, sif

Comparison: element wise product, something custom, abs difference

- Classifier

Takes the attribute similarity representations and uses as features for classifier

---

# Recommendations

Problem: design recommendations for a given platform (Netflix)

## Requirements

1. Hundreds of millions of subscribers
2. 10s of millions DAU (daily active users)

## Goals

### Business Goal

Given a user at a given time, predict which movies should be recommended

### ML Problem

Predict the probability an user will engage with xxx movies and rank them accordingly

1. Use implicit feedback (clicked on / viewed movie) vs explicit feedback (star ranking)

### Metrics

Online Metrics

- Engagement rate: (sessions w/ clicks / total number of sessions)
- Videos watched
- Session watch time

Offline metrics

- Mean Average Precision

## Collaborative Filtering

- Suggests candidates based solely on historical interaction of users
- Uses similarities between queries and items simultaneously

Weighted Matrix Factorization for solving embedding matrix

Adv

- Does not require domain knowledge to create user and media profiles

- Can help recommend new interests

Disadv

- Suffers from cold start problem

- Hard to include side features like country, age

Two step process:

1. Nearest neighorhood - create matrix of users vs movies and do similiarity

2. Matrix factorization

## Content Based Filtering

- Used to create and match user profiles with media profiles

- Uses similarities between items to recommend similar items

Adv

- Model doesnt need any data about other users, makes it easier to scale to large number of users

- Model can capture specific interest of a user, and can recommend niche items that very few other users are interested in

DisAdv

- Since feature representations are hand-engineered, requires lots of domain knowledge

- Model can only make recommendations based on existing interests of suer

1. movies x attributes matrix (TF)

2. Normalise TF vector

3. TF-IDF matrix

## Features

1. Media-user features

- user movie embedding

- user age match

2. User-based features

- average session time

- language

- age

- gender

3. Context-based features

- season of the year

- upcoming holiday

- time of day

- day of week

4. Media-based features

- release year

- public platform rating

- content tags

- movie duration

## Additional

1. Weight training examples

- Weight based on content watch time

2. Downsample negative training examples

3. Predict out of time zone

## Steps

1. Candidate generation based on movies, user context, and training data

   - focus on high recall, or focus on gathering most movies that might interest user

   - features based on user (age/gender/language), context (time, day, holidays), media (release year, rantings, duration), demographic (similiar user recommendations)

2. Return candidates from candidate generation

3. Build ranker model

   - focus on high precision, rank the top k predictions

4. Return recommendations

5. Display on homepage