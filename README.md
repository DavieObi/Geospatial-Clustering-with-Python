# 🗺️ Geospatial Clustering with Python

## 📖 Project Overview

This project focused on performing **geospatial clustering** using Python to analyze and visualize delivery locations across India. The goal was to identify meaningful regional delivery zones by leveraging **geographical coordinates**, **real-world distance computation**, and **K-Means clustering**, thereby uncovering spatial patterns that could assist in **logistics optimization**, **route planning**, and **service expansion**.

---

## 🧩 Objectives

* To calculate real-world distances between restaurants and delivery destinations.
* To visualize delivery activity across India using interactive maps.
* To apply K-Means clustering on delivery coordinates to identify geographic zones.
* To interpret and label valid delivery zones for operational insights.

---

## 🧠 Tech Stack

| Category             | Tools/Libraries       |
| -------------------- | --------------------- |
| Programming Language | Python                |
| Data Analysis        | Pandas, NumPy         |
| Clustering           | Scikit-learn (KMeans) |
| Geospatial Distance  | Geopy (geodesic)      |
| Visualization        | Plotly (Scattergeo)   |

---

## 📂 Dataset Description

The dataset, **train.csv**, contained information about delivery operations, including:

| Column                                                       | Description                                |
| ------------------------------------------------------------ | ------------------------------------------ |
| `Delivery_person_ID`                                         | Unique identifier for each delivery person |
| `Restaurant_latitude` & `Restaurant_longitude`               | Coordinates of the pickup location         |
| `Delivery_location_latitude` & `Delivery_location_longitude` | Coordinates of the delivery location       |
| `Road_traffic_density`, `Weatherconditions`                  | Environmental factors affecting delivery   |
| `Time_taken(min)`                                            | Time taken to complete the delivery        |
| *(and several others)*                                       | Delivery-specific metadata                 |

---

## ⚙️ Methodology

### **1. Distance Calculation**

We calculated the **real-world geographic distance** between each restaurant and delivery destination using the **Geodesic method** from the `geopy` library:

```python
def calculate_distance(row):
    return geodesic(
        (row['Restaurant_latitude'], row['Restaurant_longitude']),
        (row['Delivery_location_latitude'], row['Delivery_location_longitude'])
    ).km

data['Distance_km'] = data.apply(calculate_distance, axis=1)
```

This computation added a new feature, `Distance_km`, representing the delivery trip length in kilometers.

---

### **2. Visualizing Delivery Distribution**

We used **Plotly** to plot all delivery locations across India on an **interactive geographical map**:

```python
fig.add_trace(go.Scattergeo(
    lon=data['Delivery_location_longitude'],
    lat=data['Delivery_location_latitude'],
    mode='markers',
    marker=dict(color='blue', size=6, opacity=0.7),
    name='Delivery Locations'
))
```

#### **Observation:**

We observed that most delivery points were concentrated in **southern and central India**, particularly around **Karnataka, Tamil Nadu, and Maharashtra**. Northern and northeastern regions had fewer deliveries, suggesting potential areas for future expansion.

---

### **3. K-Means Clustering**

We performed **K-Means clustering** on the delivery coordinates to identify geospatial groupings:

```python
X = data[['Delivery_location_latitude', 'Delivery_location_longitude']]
kmeans = KMeans(n_clusters=3, random_state=42)
data['Cluster'] = kmeans.fit_predict(X)
centroids = kmeans.cluster_centers_
```

We visualized the clusters and centroids using another **Plotly Scattergeo** plot.

#### **Observation:**

* **Cluster 0 (Blue):** Represented the **Central Delivery Zone**, covering Maharashtra and Madhya Pradesh.
* **Cluster 2 (Green):** Represented the **Southern Delivery Zone**, including Tamil Nadu and Karnataka.
* **Cluster 1:** Contained **outliers** or invalid coordinates that lay outside India’s geographical boundaries, likely due to GPS or data entry errors.

---

### **4. Outlier Removal and Zone Labeling**

We filtered out the invalid cluster and mapped meaningful zone names:

```python
filtered_data = data[data['Cluster'] != 1]
filtered_centroids = centroids[[0, 2]]

cluster_labels = {
    0: "Central Delivery Zone",
    2: "Southern Delivery Zone"
}
filtered_data['Optimized_Zone'] = filtered_data['Cluster'].map(cluster_labels)
```

This allowed us to label valid delivery regions and enhance interpretability.

---

## 📊 Summary of Insights

1. **Distance Patterns:**
   Average delivery distances were moderate, typically within a few kilometers, aligning with standard urban delivery operations.

2. **Regional Concentration:**
   Most deliveries occurred in **southern** and **central** parts of India, while the **north and northeast** showed sparse activity.

3. **Operational Zones:**

   * **Central Delivery Zone:** Maharashtra and Madhya Pradesh — moderate traffic, central hub distribution.
   * **Southern Delivery Zone:** Tamil Nadu and Karnataka — high-density, high-frequency delivery routes.

4. **Data Quality Issue:**
   Cluster 1 revealed coordinates falling outside India, highlighting the importance of **geospatial validation** during data preprocessing.

---

## 🧭 Conclusion

Through this project, we successfully applied **geospatial analytics and clustering techniques** to derive actionable business insights from delivery data.

We identified two valid and high-performing operational zones — **Central** and **Southern Delivery Zones** — which can be leveraged for **optimized delivery routing**, **resource allocation**, and **strategic expansion**.

This analysis demonstrated how **data-driven geospatial clustering** can transform raw coordinate data into **intelligent delivery zones**, contributing directly to logistical efficiency and business growth.

---

## 🚀 Future Improvements

* Integrate **DBSCAN** for better handling of outliers.
* Use **traffic and weather** variables in a multivariate clustering approach.
* Incorporate **time-based patterns** (peak vs. off-peak hours).
* Deploy the final interactive visualization using **Streamlit or Dash**.

---
