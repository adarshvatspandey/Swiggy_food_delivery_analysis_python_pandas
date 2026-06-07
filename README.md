# 🍽️ Food Delivery Analytics Using Python & Pandas

## 📌 Project Overview

This project analyzes 197,430 food delivery records to uncover business insights related to restaurant performance, customer preferences, dish popularity, pricing strategies, and market trends. The analysis was performed using Python, Pandas, NumPy, and Matplotlib.

---

## 📊 Dataset Information

| Column | Description |
|----------|------------|
| State | State Name |
| City | City Name |
| Order Date | Date of Order |
| Restaurant Name | Restaurant Name |
| Location | Restaurant Locality |
| Category | Food Category |
| Dish Name | Dish Name |
| Price (INR) | Dish Price |
| Rating | Customer Rating |
| Rating Count | Number of Ratings |

### Dataset Size
- Total Records: 197,430
- Total Features: 10

---

## 🛠️ Technologies Used

- Python
- Pandas
- NumPy
- Matplotlib
- Jupyter Notebook

---

# 📈 Business Questions, Solutions & Insights

## 1. Top Restaurants by Weighted Rating

### Question
Which restaurants have the best combination of high ratings and high review volume?

### Solution

```python
restaurant_score = (
    df.groupby("Restaurant Name")
      .agg(
          avg_rating=("Rating","mean"),
          total_reviews=("Rating Count","sum")
      )
)

restaurant_score["weighted_score"] = (
    restaurant_score["avg_rating"] *
    np.log1p(restaurant_score["total_reviews"])
)

top_restaurants = restaurant_score.sort_values(
    "weighted_score",
    ascending=False
).head(10)

print(top_restaurants)
```

### Insight
Restaurants with high ratings and large review counts are generally more reliable and trusted by customers.

---

## 2. Most Popular Dishes

### Question
Which dishes receive the highest customer engagement?

### Solution

```python
popular_dishes = (
    df.groupby("Dish Name")["Rating Count"]
      .sum()
      .sort_values(ascending=False)
      .head(10)
)

print(popular_dishes)
```

### Insight
Popular dishes contribute significantly to customer engagement and restaurant visibility.

---

## 3. Premium Dish Analysis

### Question
Which expensive dishes maintain excellent customer ratings?

### Solution

```python
premium_dishes = (
    df.groupby("Dish Name")
      .agg(
          avg_price=("Price (INR)","mean"),
          avg_rating=("Rating","mean")
      )
      .query("avg_price > 300")
      .sort_values("avg_rating",ascending=False)
      .head(10)
)

print(premium_dishes)
```

### Insight
Premium pricing works best when paired with high product quality and customer satisfaction.

---

## 4. Monthly Revenue Trend

### Question
How does revenue vary over time?

### Solution

```python
monthly_revenue = (
    df.groupby(df["Order Date"].dt.to_period("M"))
      ["Price (INR)"]
      .sum()
)

print(monthly_revenue)
```

### Insight
Monthly trends reveal seasonality, peak demand periods, and business growth opportunities.

---

## 5. City-wise Average Food Price

### Question
Which cities have the highest average food prices?

### Solution

```python
city_price = (
    df.groupby("City")
      ["Price (INR)"]
      .mean()
      .sort_values(ascending=False)
)

print(city_price)
```

### Insight
Some cities support premium pricing due to stronger purchasing power and demand.

---

## 6. Restaurant Diversity Index

### Question
Which restaurants offer the widest variety of dishes?

### Solution

```python
menu_diversity = (
    df.groupby("Restaurant Name")
      ["Dish Name"]
      .nunique()
      .sort_values(ascending=False)
      .head(10)
)

print(menu_diversity)
```

### Insight
Restaurants with diverse menus can appeal to a wider customer base.

---

## 7. Price vs Rating Correlation

### Question
Do expensive dishes receive higher ratings?

### Solution

```python
correlation = df["Price (INR)"].corr(df["Rating"])

print("Correlation:", correlation)
```

### Insight
The correlation helps determine whether higher prices influence customer satisfaction.

---

## 8. Top Restaurant in Every City

### Question
Which restaurant performs best in each city?

### Solution

```python
top_city_restaurants = (
    df.groupby(["City","Restaurant Name"])
      ["Rating"]
      .mean()
      .reset_index()
      .sort_values(
          ["City","Rating"],
          ascending=[True,False]
      )
      .drop_duplicates("City")
)

print(top_city_restaurants)
```

### Insight
Identifies market leaders and top-performing restaurants in each city.

---

## 9. Category Performance Analysis

### Question
How do food categories perform across ratings and popularity?

### Solution

```python
category_performance = (
    df.groupby("Category")
      .agg(
          avg_price=("Price (INR)","mean"),
          avg_rating=("Rating","mean"),
          total_reviews=("Rating Count","sum")
      )
      .sort_values("total_reviews",ascending=False)
)

print(category_performance)
```

### Insight
Some food categories attract more customers despite lower average prices.

---

## 10. Hidden Gems Analysis

### Question
Which highly-rated dishes receive limited customer attention?

### Solution

```python
hidden_gems = (
    df.groupby("Dish Name")
      .agg(
          avg_rating=("Rating","mean"),
          reviews=("Rating Count","sum")
      )
      .query("avg_rating >= 4.7 and reviews < 500")
      .sort_values("avg_rating",ascending=False)
)

print(hidden_gems.head(10))
```

### Insight
Hidden gems represent opportunities for increased promotion and visibility.

---

## 11. Customer Demand Hotspots

### Question
Which food categories dominate each city?

### Solution

```python
city_category = (
    df.pivot_table(
        values="Rating Count",
        index="City",
        columns="Category",
        aggfunc="sum",
        fill_value=0
    )
)

print(city_category)
```

### Insight
Customer preferences vary significantly across different cities.

---

## 12. Price Segmentation Analysis

### Question
How do ratings vary across different price ranges?

### Solution

```python
df["Price Segment"] = pd.cut(
    df["Price (INR)"],
    bins=[0,100,200,300,500,1000],
    labels=[
        "Budget",
        "Economy",
        "Standard",
        "Premium",
        "Luxury"
    ]
)

segment_analysis = (
    df.groupby("Price Segment")
      .agg(
          avg_price=("Price (INR)","mean"),
          avg_rating=("Rating","mean")
      )
)

print(segment_analysis)
```

### Insight
Helps determine whether premium-priced dishes actually deliver better customer experiences.

---

## 13. Restaurant Market Share Analysis

### Question
Which restaurants dominate customer engagement?

### Solution

```python
market_share = (
    df.groupby("Restaurant Name")
      ["Rating Count"]
      .sum()
      .sort_values(ascending=False)
      .head(10)
)

print(market_share)
```

### Insight
A small number of restaurants often account for a large percentage of customer attention.

---

## 14. Best Value-for-Money Dishes

### Question
Which dishes provide the highest satisfaction per rupee spent?

### Solution

```python
value_dishes = (
    df.groupby("Dish Name")
      .agg(
          avg_rating=("Rating","mean"),
          avg_price=("Price (INR)","mean")
      )
)

value_dishes["Value Score"] = (
    value_dishes["avg_rating"] /
    value_dishes["avg_price"]
)

best_value = value_dishes.sort_values(
    "Value Score",
    ascending=False
).head(10)

print(best_value)
```

### Insight
These dishes deliver excellent customer satisfaction while remaining affordable.

---

# 🎯 Overall Project Insights

- Highly-rated restaurants tend to maintain strong customer engagement.
- Dish popularity is concentrated among a small number of menu items.
- Premium pricing does not always guarantee better ratings.
- Customer preferences vary across cities and categories.
- Hidden gems exist that deserve greater visibility.
- Market share is concentrated among a few dominant restaurants.
- Value-for-money dishes often generate strong customer loyalty.

---

# 🚀 Future Enhancements

- Interactive Dashboard using Power BI
- Restaurant Recommendation System
- Customer Rating Prediction Model
- Sales Forecasting using Machine Learning
- Geographic Demand Analysis

