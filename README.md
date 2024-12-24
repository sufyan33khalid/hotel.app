# hotel.app
pip install pandas streamlit matplotlib seaborn
import pandas as pd
import streamlit as st
import matplotlib.pyplot as plt
import seaborn as sns

# Load the dataset (replace with your dataset path)
uploaded_file = st.file_uploader("/content/Hotel Bookings - Dummy - 2.csv", type=["csv"])
if uploaded_file:
    data = pd.read_csv(uploaded_file)

    # Data preprocessing
    st.title("Hotel Metrics Dashboard")
    
    # Define columns (replace these with actual column names from your dataset)
    revenue_column = 'rental_revenue'
    occupied_rooms_column = 'occupied_rooms'
    total_rooms_column = 'total_rooms'
    nights_column = 'nights'
    reservation_status_column = 'reservation_status'
    market_segment_column = 'market_segment'
    lead_time_column = 'lead_time'

    # Metrics Calculation
    st.header("Calculated Metrics")

    # ADR Calculation
    if revenue_column in data.columns and occupied_rooms_column in data.columns:
        data['ADR'] = data[revenue_column] / data[occupied_rooms_column]
        st.write("Average Daily Rate (ADR):", round(data['ADR'].mean(), 2))
    else:
        st.write("ADR: Data required for calculation is missing.")

    # RevPAR Calculation
    if revenue_column in data.columns and total_rooms_column in data.columns:
        data['RevPAR'] = data[revenue_column] / data[total_rooms_column]
        st.write("Revenue Per Available Room (RevPAR):", round(data['RevPAR'].mean(), 2))
    else:
        st.write("RevPAR: Data required for calculation is missing.")

    # Occupancy Calculation
    if occupied_rooms_column in data.columns and total_rooms_column in data.columns:
        data['Occupancy'] = (data[occupied_rooms_column] / data[total_rooms_column]) * 100
        st.write("Occupancy Rate (%):", round(data['Occupancy'].mean(), 2))
    else:
        st.write("Occupancy: Data required for calculation is missing.")

    # Length of Stay
    if nights_column in data.columns:
        st.write("Average Length of Stay (LOS):", round(data[nights_column].mean(), 2))
    else:
        st.write("Length of Stay: Data column is missing.")

    # Cancellation Analysis
    if reservation_status_column in data.columns:
        cancellations = data[reservation_status_column].value_counts().get('Canceled', 0)
        confirmed = data[reservation_status_column].value_counts().get('Confirmed', 0)
        st.write("Cancellation Ratio:", round(cancellations / (cancellations + confirmed) * 100, 2), "%")
    else:
        st.write("Cancellation Analysis: Data column is missing.")

    # Market Segment Analysis
    if market_segment_column in data.columns:
        st.header("Market Segment Analysis")
        market_segment_data = data[market_segment_column].value_counts()
        st.bar_chart(market_segment_data)
    else:
        st.write("Market Segment Analysis: Data column is missing.")

    # Lead Time Analysis
    if lead_time_column in data.columns:
        st.header("Lead Time Analysis")
        lead_time_bins = pd.cut(data[lead_time_column], bins=[0, 3, 7, 14, 30, 90], labels=['1-3 days', '4-7 days', '8-14 days', '15-30 days', '>30 days'])
        lead_time_data = lead_time_bins.value_counts().sort_index()
        st.bar_chart(lead_time_data)
    else:
        st.write("Lead Time Analysis: Data column is missing.")

    # Visualizations
    st.header("Visualizations")
    fig, ax = plt.subplots()
    sns.heatmap(data.corr(), annot=True, cmap='coolwarm', ax=ax)
    st.pyplot(fig)

    st.write("Explore more insights by uploading data!")

# Run the Streamlit app using `streamlit run <filename>.py`
