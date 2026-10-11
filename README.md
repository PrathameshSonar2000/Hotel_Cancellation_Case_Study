# Hotel Cancellation Case Study

## Overview
This project analyzes hotel booking cancellation behavior using a real-world hotel booking dataset. The objective is to understand why customers cancel reservations and to identify the variables that most strongly influence cancellation risk. The analysis combines exploratory data analysis (EDA), chart-based interpretation, and business-focused recommendations for reducing cancellations and improving revenue management.

The repository contains the dataset, the exploratory notebook, and the supporting project documentation. The work is designed to help hotel managers, revenue teams, and data analysts assess cancellation risk and build strategies for booking retention.

## Project Objective
The main business question is:

- What patterns and characteristics are associated with hotel booking cancellations?
- Which booking features are strongest predictors of cancellation risk?
- How can hotels reduce cancellation losses while maintaining demand and occupancy?

## Business Context
Hotel cancellations are a major operational and revenue challenge. Excessive cancellations can reduce expected occupancy, cause lost revenue, affect staffing, and disrupt room allocation. Understanding whether a booking is likely to be canceled enables hotels to take proactive steps such as:

- offering flexible cancellation policies for high-risk segments,
- targeting customers with reminders and confirmations,
- personalizing pricing strategies,
- managing overbooking and demand forecasting more effectively.

## Data Source
The primary dataset is:

- `data/hotel_bookings.csv`
- `data/hotel_bookings_cleaned.csv`

The dataset contains hotel booking information across multiple years, including booking lead time, guest type, length of stay, room rate, and cancellation status.

## Repository Structure

```text
Hotel_Cancellation_Case_Study/
├── data/
│   ├── hotel_bookings.csv
│   └── hotel_bookings_cleaned.csv
├── notebooks/
│   └── EDA.ipynb
├── README.md
├── .gitignore
└── requirements.txt (if added separately in future)
```

## Tools and Technologies
- Python
- Pandas
- NumPy
- Seaborn
- Matplotlib
- Jupyter Notebook

## Data Cleaning and Preparation
A cleaned dataset was prepared for analysis before modeling and visualization. The workflow included:

- loading the raw hotel booking dataset,
- checking data types and missing values,
- validating cancellation labels,
- calculating derived variables such as `total_nights`,
- filtering extreme outliers for graphical clarity where required,
- preparing the dataset for exploratory and multivariate analysis.

## Exploratory Data Analysis (EDA)

### 1) Distribution of Booking Cancellations
Chart: `sns.countplot(data=df, x="is_canceled", palette="pastel")`

Logic used:
- Count how many bookings were canceled versus not canceled.
- Segment the dataset to understand the imbalance in the outcome variable.

Interpretation:
> Hotel booking data reveals over 75,000 confirmed stays compared to more than 40,000 cancellations. While confirmed bookings represent the majority, a cancellation volume this high is unusual since cancellations typically signal a significant risk to revenue and operational planning.

Conclusion:
- The dataset shows a substantial cancellation rate, making the problem operationally relevant.
- Cancellation risk should be treated as a key revenue management issue rather than a minor data anomaly.

---

### 2) Distribution of Booking Lead Time
Chart: `sns.histplot(df["lead_time"], kde=True, bins=50, color="skyblue")`

Logic used:
- Plot the distribution of booking lead time (number of days between reservation and arrival).
- Highlight the shape of the distribution and identify extreme outliers.

Interpretation:
> The first plot clearly shows that while most bookings occur within 0 to 100 days, a tiny fraction of extreme bookings stretches out to 400–650 days, heavily compressing the main distribution. Between 0 and 100 days, the density is extremely high, which means most reservations are made relatively close to the stay date, while long-lead bookings are rare but influential.

Conclusion:
- Lead time is a highly informative variable for cancellation behavior.
- A small subset of very early bookings can distort the visual scale of the distribution, so outlier handling is important.

---

### 3) Lead Time Distribution with IQR Outlier Filtering
Chart: `sns.histplot(data=df1, x="lead_time", kde=True, bins=50, color="skyblue", ax=axes[1])`

Logic used:
- Apply the IQR rule:
  - `Q1 = np.percentile(df['lead_time'], 25)`
  - `Q3 = np.percentile(df['lead_time'], 75)`
  - `df1 = df[df['lead_time'] < Q3 + 1.5 * (Q3 - Q1)]`
- Replot the distribution after removing extreme observations.

Interpretation:
- The filtered plot provides a clearer view of the dominant booking pattern.
- The main distribution strongly clusters between short and mid-range lead times.

Conclusion:
- Most bookings are made within a moderate time window, which supports the idea that cancellation risk is concentrated in certain booking time patterns rather than appearing uniformly.

---

### 4) Booking Distribution by Year
Charts:
- `sns.countplot(data=df, x="arrival_date_year", palette="muted", ax=axes[0])`
- `sns.countplot(data=df, x="arrival_date_year", hue='is_canceled', palette="muted", ax=axes[1])`

Logic used:
- Compare booking counts across years.
- Separate canceled and non-canceled bookings by year to see whether cancellation patterns remain stable or vary over time.

Interpretation:
- Booking volume changes by year, and cancellation behavior appears to vary in absolute numbers.
- The analysis indicates that some variation exists across years, but the overall cancellation pattern remains meaningful in each period.

Conclusion:
- Year is a useful context variable, but the dominant drivers of cancellations are likely not calendar year alone.
- More important patterns are likely to be found in guest behavior, stay characteristics, and booking timing.

---

### 5) Lead Time Distribution by Cancellation Status
Charts:
- `sns.boxplot(data=df, x="is_canceled", y="lead_time", showfliers=False, palette="Set2", ax=ax1)`
- `sns.boxplot(data=df, x="is_canceled", y="lead_time", showfliers=True, palette="Set2", ax=ax2)`

Logic used:
- Compare lead time distributions for canceled vs. non-canceled bookings.
- Use both filtered and unfiltered box plots to see the central tendency and the effect of outliers.

Interpretation:
> Shorter lead times (0 to 150 days) represent firm plans with a low probability of cancellation, showing much tighter variation on a box plot. In contrast, longer lead times introduce uncertainty, and they are more closely associated with cancellations. This pattern suggests that bookings made far in advance are more likely to be altered, canceled, or re-planned before arrival.

Conclusion:
- Higher lead times are associated with a higher risk of cancellation.
- Early reservations should be considered a priority segment for retention and follow-up actions.

---

### 6) Total Nights vs ADR Scatter Plot
Chart:
```python
sns.scatterplot(
    data=df[df["adr"] < 400],
    x="total_nights",
    y="adr",
    hue="is_canceled",
    alpha=0.6,
)
```

Logic used:
- Create the derived variable `total_nights = stays_in_weekend_nights + stays_in_week_nights`.
- Plot the relationship between stay duration and average daily rate.
- Color by cancellation status to reveal whether longer stays are more likely to be canceled.

Interpretation:
> A distinct pattern emerges when comparing total nights stayed against the Average Daily Rate (ADR). Bookings that exceed 10 nights show a high frequency of cancellations.

Conclusion:
- Longer stays are more likely to be canceled than short stays.
- Hotels should treat long-stay reservations as higher-risk transactions and manage them with proactive retention measures.

---

### 7) Correlation Matrix of Numeric Features
Chart:
`sns.heatmap(df[numeric_cols].corr(), annot=True, fmt=".2f", cmap="coolwarm")`

Logic used:
- Compute the Pearson correlation matrix for numeric columns.
- Identify which variables move together and which ones have a meaningful link with cancellation behavior.

Key insights from the notebook:

1. The Core Metrics of "Total Nights Stayed"

- `total_nights` vs. `stays_in_week_nights` = `0.94`
- `total_nights` vs. `stays_in_weekend_nights` = `0.76`

Interpretation:
> These extremely high positive numbers are expected because total_nights is directly calculated from them.

2. Key Insights for Cancellations (`is_canceled`)

- `is_canceled` vs. `lead_time` = `0.29`
- `is_canceled` vs. `total_special_requests` = `-0.23`
- `is_canceled` vs. `required_car_parking_spaces` = `-0.20`

Interpretation:
> `is_canceled` vs. `lead_time` (0.29): This is one of the strongest positive indicators for cancellations in the dataset. It means that the earlier a booking is made (higher lead time), the more likely it is to be canceled.

> `is_canceled` vs. `total_special_requests` (-0.23): This negative relationship indicates that guests who make special requests are significantly less likely to cancel.

> `is_canceled` vs. `required_car_parking_spaces` (-0.20): Another negative relationship. Guests who explicitly request a parking spot almost never cancel their trips.

3. Customer Loyalty and Repeat Bookings

- `is_repeated_guest` vs. `previous_bookings_not_canceled` = `0.42`
- `is_repeated_guest` vs. `previous_cancellations` = `0.08`

Interpretation:
> There is a moderate positive correlation between repeat guests and prior non-cancelled bookings. Regular, returning customers have a solid track record of completing their stays without cancellation.

4. Price & Guest Demographics (`adr`)

- `adr` vs. `adults` = `0.23`
- `adr` vs. `children` = `0.32`
- `adr` vs. `is_repeated_guest` = `-0.13`

Interpretation:
> The price of the room increases when more people—especially children—are added to the booking. Returning loyal customers may receive lower rates or discounts.

Conclusion:
- The correlation matrix confirms that lead time, stay duration, special requests, parking requests, and repeat guest status are among the most meaningful signals for cancellation behavior.

---

### 8) Customer Type Distribution by Cancellation Status
Chart: `sns.countplot(data=df, x="customer_type", hue='is_canceled', palette="muted")`

Logic used:
- Compare the number of bookings by customer type.
- See whether different customer segments have different cancellation rates.

Interpretation:
- Certain customer types have stronger cancellation tendencies than others.
- The chart suggests that transient customers dominate the dataset and are likely among the main source of cancellation volume.

Conclusion:
- Customer type should be included in cancellation risk analysis and pricing strategies.
- More targeted retention policies may be needed for transient and high-risk segments.

---

## Hypothesis Interpretation and Conclusions
The notebook does not contain formal statistical tests with p-values or hypothesis testing libraries; instead, it uses descriptive statistics, correlation analysis, and chart-based evidence. The following are the implied hypotheses and their conclusions based on the analysis.

### Hypothesis 1: Longer lead times increase cancellation likelihood.
- Evidence: `is_canceled` vs. `lead_time` correlation is `0.29`.
- Visual evidence: the boxplots show that canceled bookings tend to have longer lead times.
- Conclusion: Accept.

### Hypothesis 2: Guests with special requests are less likely to cancel.
- Evidence: `is_canceled` vs. `total_special_requests` correlation is `-0.23`.
- Conclusion: Accept.

### Hypothesis 3: Guests who request parking are less likely to cancel.
- Evidence: `is_canceled` vs. `required_car_parking_spaces` correlation is `-0.20`.
- Conclusion: Accept.

### Hypothesis 4: Longer stays have a higher likelihood of cancellation.
- Evidence: the ADR vs total nights scatter plot shows a clear concentration of cancellations for bookings exceeding 10 nights.
- Conclusion: Accept.

### Hypothesis 5: Repeat guests are more reliable and less likely to cancel.
- Evidence: `is_repeated_guest` vs. `previous_bookings_not_canceled` correlation is `0.42`.
- Conclusion: Accept.

### Overall Conclusion from the Analysis
The data supports the view that cancellations are strongly shaped by booking behavior and customer commitment. The strongest cancellation signals are:

- longer lead time,
- longer total stay duration,
- transient customer type,
- lower special request activity,
- lower likelihood of parking requests,
- weaker prior guest loyalty.

This gives hotels a data-driven basis for designing cancellation prevention strategies.

---

## Business Recommendations and Proposed Solution
The solution that follows is based on the observed patterns in the dataset and the business logic from the analysis.

### 1) Segment High-Risk Bookings
Hotels should classify reservations based on lead time and stay duration.

- High-risk: long lead time, long stay, low special requests, no parking request.
- Low-risk: short lead times, short stay, special requests, repeat guest behavior.

Action:
- flag high-risk reservations for proactive follow-up,
- target follow-up emails and reminders to customers before arrival,
- offer flexible rebooking options rather than full cancellation.

### 2) Introduce Retention Strategies for Long-Lead Reservations
Because earlier bookings are more likely to be canceled, hotels should use early intervention.

Actions:
- send confirmation reminders 7, 14, and 30 days before arrival,
- provide personalized offers for guests who are still undecided,
- encourage direct booking and loyalty benefits for long-lead reservations.

### 3) Design Pricing and Loyalty Policies for Repeat Guests
The analysis suggests repeat guests are more reliable.

Actions:
- reward frequent guests with discounts and loyalty perks,
- prioritize direct booking channels for returning customers,
- create targeted retention campaigns for repeat customers.

### 4) Use Special Requests as a Trust Signal
Bookings with special requests are associated with lower cancellation risk.

Actions:
- maintain high service quality and room customization for special-request guests,
- provide personalized communication to guests who ask for extras,
- track special-request bookings as lower-risk segments for occupancy forecasting.

### 5) Manage Long-Stay Bookings with Extra Care
Long stays have a greater chance of cancellation, especially if the trip is being planned far in advance.

Actions:
- require stronger confirmation for long-stay reservations,
- offer shorter cancellation windows or partial deposit policies for high-risk longer stays,
- reserve flexible cancellation options only for segments that justify the commercial risk.

### 6) Build a Cancellation Prediction Model
The next step is to convert these insights into a machine learning or business rule model.

Candidate features:
- `lead_time`
- `total_nights`
- `customer_type`
- `special_requests`
- `required_car_parking_spaces`
- `is_repeated_guest`
- `arrival_date_year`
- `adr`

Model objective:
- Predict cancellation probability before check-in.
- Prioritize interventions by risk score.

## Final Outcome
The project demonstrates that hotel cancellation is not random. It follows identifiable booking and customer behavior patterns. The most influential features are high lead time, long stay length, low special requests, lower parking engagement, and weaker repeat guest history. By integrating these insights into revenue operations, hotels can lower cancellation losses, improve forecasting, and enhance customer retention.

## Summary
This case study analyzes hotel booking cancellations using practical data analytics techniques and visual exploration. The project identifies the strongest operational drivers of cancellations and offers a structured business solution to mitigate them. It combines clear visual storytelling with analytics logic and decision-making guidance, making it suitable for both academic and business presentation.

## License
This project is intended for learning and analytical demonstration purposes.

## Author
Prathamesh Sonar

---

This README reflects the chart-based analysis, interpretation, and business conclusions derived from the notebook and repository analysis.
