# Understanding Your Guests: A Data-Driven Guide to Pricing, Stay Length, and Cancellations
**City Hotel Lisbon, Booking Intelligence Report**  
**Course:** Applied Machine Learning and Predictive Modelling I, HSLU  
**Team:** Petronela Tabalae · Divya Shori · Lorena Zovkic

---

## Project Overview

This project analyses **79,330 hotel booking records** from a city hotel in Lisbon (H2), covering arrivals between July 2015 and August 2017. The dataset includes both completed stays and cancellations, and was published by Antonio, Almeida, and Nunes (2019).

Monthly weather data for Lisbon (temperature, precipitation, sunshine hours) from the [Open-Meteo historical archive](https://open-meteo.com) is joined to the booking data as additional context.

Three business questions drive the analysis:

1. **Pricing** — Which booking and seasonal characteristics determine the nightly rate, and by how much?
2. **Stay Length** — Which guest segments stay longest, and how large are the differences?
3. **Cancellations** — What information available at booking time best predicts whether a reservation will be cancelled?

---

## Models

| # | Model | Question | Method |
|---|-------|----------|--------|
| 1 | Linear Model | Pricing | `lm()` on log(ADR) |
| 2 | Poisson GLM | Stay Length | `glm(family = poisson)` with overdispersion check |
| 3 | Logistic Regression | Cancellation (baseline) | `glm(family = binomial)` |
| 4 | Generalised Additive Model | Cancellation (nonlinear) | `mgcv::gam()` |
| 5 | Support Vector Machine | Cancellation (interactions) | `kernlab::ksvm()` with subsample sensitivity check |
| 6 | Neural Network | Cancellation (flexible) | `nnet::nnet()` |

All models trained on 80% of the data and evaluated on the same held-out 20% test set. Models 3–6 use an identical predictor set — Lead Time, Deposit Type, Market Segment, Previous Cancellations, Special Requests, Repeat Guest, and Monthly Rainfall — ensuring any performance differences reflect model structure only.

---

## Repository Structure

```
ML1/
├── H2.csv                        # Hotel booking dataset (source below)
├── weather_lisbon.rds            # Cached Lisbon monthly weather data
├── hotel_booking_report.Rmd      # Main analysis — knit this to reproduce
├── hotel_booking_report.html     # Rendered HTML report (submission ready)
├── README.md
├── LICENSE
└── .gitignore
```

---

## How to Run

1. Open the project in RStudio
2. Ensure `H2.csv` and `weather_lisbon.rds` are in the project root
3. Knit `hotel_booking_report.Rmd` to HTML

> If `weather_lisbon.rds` is missing, the script will fetch it automatically from the Open-Meteo API on first run and cache it locally.

**Required R packages:**  
`tidyverse`, `httr`, `jsonlite`, `lubridate`, `car`, `multcomp`, `broom`, `kableExtra`, `scales`, `patchwork`, `caret`, `pROC`, `mgcv`, `kernlab`, `PRROC`, `nnet`

---

## Dataset

The analysis uses **H2 only** (City Hotel, Lisbon). Both hotels in the original publication are located in Portugal: H1 at the resort region of Algarve and H2 in the city of Lisbon.

**Source:** Antonio, N., de Almeida, A., & Nunes, L. (2019). Hotel booking demand datasets. *Data in Brief*, 22, 41–49.  
https://www.sciencedirect.com/science/article/pii/S2352340918315191

Since this is real hotel data, all elements pertaining to hotel or customer identification have been deleted.

---

## Authors

| Name | Contribution |
|------|-------------|
| Divya Shori | Models 1 & 2 (Linear Model, Poisson GLM), EDA |
| Lorena Zovkic | Models 3 & 4 (Logistic Regression, GAM), data preparation |
| Petronela Tabalae | Models 5 & 6 (SVM, Neural Network), model comparison |

---

## Data Dictionary

| Variable | Type | Description |
|:---------|:-----|:------------|
| hotel | character | Hotel (H1 = Resort Hotel or H2 = City Hotel) |
| is_canceled | double | Value indicating if the booking was canceled (1) or not (0) |
| lead_time | double | Number of days elapsed between booking entry into PMS and arrival date |
| arrival_date_year | double | Year of arrival date |
| arrival_date_month | character | Month of arrival date |
| arrival_date_week_number | double | Week number of year for arrival date |
| arrival_date_day_of_month | double | Day of arrival date |
| stays_in_weekend_nights | double | Number of weekend nights (Saturday or Sunday) the guest stayed or booked to stay |
| stays_in_week_nights | double | Number of week nights (Monday to Friday) the guest stayed or booked to stay |
| adults | double | Number of adults |
| children | double | Number of children |
| babies | double | Number of babies |
| meal | character | Meal package: SC/Undefined = no meal; BB = Bed & Breakfast; HB = Half board; FB = Full board |
| country | character | Country of origin (ISO 3155-3:2013 format) |
| market_segment | character | Market segment. "TA" = Travel Agents, "TO" = Tour Operators |
| distribution_channel | character | Booking distribution channel. "TA" = Travel Agents, "TO" = Tour Operators |
| is_repeated_guest | double | Whether the booking name was from a repeated guest (1) or not (0) |
| previous_cancellations | double | Number of previous bookings cancelled by the customer |
| previous_bookings_not_canceled | double | Number of previous bookings not cancelled by the customer |
| reserved_room_type | character | Code of room type reserved (anonymised) |
| assigned_room_type | character | Code of room type assigned (may differ from reserved due to operations or customer request) |
| booking_changes | double | Number of changes made to the booking between entry and check-in/cancellation |
| deposit_type | character | No Deposit = no deposit made; Non Refund = deposit equal to total stay cost; Refundable = deposit below total stay cost |
| agent | character | ID of the travel agency that made the booking |
| company | character | ID of the company/entity responsible for the booking (anonymised) |
| days_in_waiting_list | double | Number of days the booking was in the waiting list before confirmation |
| customer_type | character | Contract / Group / Transient / Transient-party |
| adr | double | Average Daily Rate — total lodging transactions divided by total staying nights |
| required_car_parking_spaces | double | Number of car parking spaces required |
| total_of_special_requests | double | Number of special requests made (e.g. twin bed, high floor) |
| reservation_status | character | Last status: Canceled / Check-Out / No-Show |
| reservation_status_date | double | Date at which the last status was set |
