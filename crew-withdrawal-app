import streamlit as st
import pandas as pd
import plotly.express as px
from datetime import datetime

st.set_page_config(page_title="Crew Withdrawal Tracker & Statistics", layout="wide")

st.title("⚓ Veritas Maritime Corporation - Crew Withdrawal Tracker")
st.markdown("Record crew withdrawals, view yearly comparative metrics, and print operational reports.")

DATA_FILE = "withdrawal2026.xlsx"

@st.cache_data
def load_data():
    try:
        raw_df = pd.read_excel(DATA_FILE, sheet_name="2026")
        data_rows = raw_df.iloc[5:].copy()
        cols = [
            "No", "Date", "Empty1", "Status", "Date_Last_Exp", "Name", "Age", "Classification", 
            "Rank", "Last_Vessel", "No_Contracts", "School", "Reason_Withdrawal", "Years_Exp", 
            "Empty2", "Salary_Diff", "Higher_License", "JISS_License", "US_Visa", "MOU", 
            "NFR_Withdraw", "Empty3", "Empty4", "Division", "Principal", "Type_Withdrawal", 
            "Contact_Number", "Address", "Remarks", "Empty5", "Empty6"
        ]
        if len(data_rows.columns) == len(cols):
            data_rows.columns = cols
        data_rows = data_rows.dropna(subset=["Name"]).copy()
        data_rows["Year"] = 2026
        return data_rows
    except Exception:
        return pd.DataFrame(columns=[
            "No", "Date", "Name", "Age", "Classification", "Rank", "Last_Vessel", 
            "No_Contracts", "School", "Reason_Withdrawal", "Higher_License", 
            "JISS_License", "US_Visa", "MOU", "NFR_Withdraw", "Division", 
            "Principal", "Type_Withdrawal", "Contact_Number", "Address", "Remarks", "Year"
        ])

if "df" not in st.session_state:
    st.session_state.df = load_data()

tab1, tab2, tab3 = st.tabs(["📝 Data Entry Form", "📊 Yearly Statistics & Charts", "🖨️ Printable Report"])

with tab1:
    st.header("Log New Crew Withdrawal")
    with st.form("entry_form", clear_on_submit=True):
        col1, col2, col3 = st.columns(3)
        with col1:
            entry_date = st.date_input("Date", datetime.today())
            name = st.text_input("Full Name (LASTNAME, FIRSTNAME)")
            age = st.number_input("Age", min_value=18, max_value=80, value=30)
            classification = st.selectbox("Classification", ["Regular Crew", "SCHOLAR MAAP-IMMAJ", "FAST TRACK", "SOT", "VMC RATINGS PROGRAM"])
            rank = st.text_input("Rank (e.g., C/E, 3AE, O/S, BSN, A/B)")
            division = st.selectbox("Division", ["PCC", "BULK", "CONTAINER", "TANKER", "OTHER"])
        
        with col2:
            last_vessel = st.text_input("Last Vessel")
            no_contracts = st.number_input("No. of Contracts", min_value=0, max_value=50, value=1)
            school = st.text_input("School / Academy")
            reason = st.selectbox("Reason for Withdrawal", [
                "TRANSFER TO OTHER COMPANY", "RETIRED", "CHANGE CAREER", 
                "STOP SAILING SINCE 2021", "STOP SAILING SINCE 2023", "MEDICAL CONDITION", "PERSONAL REASON", "OTHER"
            ])
            principal = st.text_input("Principal (e.g., KSK, KRBS)")
            type_withdrawal = st.selectbox("Type of Withdrawal", ["Non Salary Related", "Salary Related", "Retired", "Medical", "Other"])
            
        with col3:
            higher_lic = st.selectbox("Higher License", ["YES", "NO", "NO DATA"])
            jiss_lic = st.selectbox("JISS License", ["YES", "NO", "NO DATA"])
            us_visa = st.selectbox("US Visa", ["YES", "NO", "NO DATA"])
            mou = st.selectbox("MOU", ["YES", "NO", "NO DATA"])
            nfr_withdraw = st.selectbox("NFR / Withdrawal Status", ["WITHDRAW", "RETIRED", "NFR"])
            contact = st.text_input("Contact Number")
            remarks = st.text_area("Remarks", "PENDING SIGNATURE")
            
        submitted = st.form_submit_button("Submit Entry")
        if submitted:
            new_row = {
                "No": len(st.session_state.df) + 1,
                "Date": entry_date.strftime("%b %d %Y").upper(),
                "Name": name,
                "Age": age,
                "Classification": classification,
                "Rank": rank,
                "Last_Vessel": last_vessel,
                "No_Contracts": no_contracts,
                "School": school,
                "Reason_Withdrawal": reason,
                "Higher_License": higher_lic,
                "JISS_License": jiss_lic,
                "US_Visa": us_visa,
                "MOU": mou,
                "NFR_Withdraw": nfr_withdraw,
                "Division": division,
                "Principal": principal,
                "Type_Withdrawal": type_withdrawal,
                "Contact_Number": contact,
                "Remarks": remarks,
                "Year": entry_date.year
            }
            st.session_state.df = pd.concat([st.session_state.df, pd.DataFrame([new_row])], ignore_index=True)
            st.success(f"Recorded withdrawal for {name}!")

with tab2:
    st.header("Yearly Comparative Statistics")
    df_curr = st.session_state.df
    
    m1, m2, m3, m4 = st.columns(4)
    m1.metric("Total Withdrawals", len(df_curr))
    m2.metric("Average Age", f"{pd.to_numeric(df_curr['Age'], errors='coerce').mean():.1f} yrs")
    m3.metric("Avg. Contracts", f"{pd.to_numeric(df_curr['No_Contracts'], errors='coerce').mean():.1f}")
    m4.metric("Top Reason", df_curr["Reason_Withdrawal"].mode()[0] if not df_curr.empty else "N/A")
    
    st.divider()
    
    c1, c2 = st.columns(2)
    with c1:
        fig_reason = px.pie(df_curr, names="Reason_Withdrawal", title="Withdrawal Causes Breakdown", hole=0.3)
        st.plotly_chart(fig_reason, use_container_width=True)
    with c2:
        fig_div = px.bar(df_curr, x="Division", color="Type_Withdrawal", title="Withdrawals by Division & Category", barmode="group")
        st.plotly_chart(fig_div, use_container_width=True)

with tab3:
    st.header("Printable Executive Summary")
    printable_cols = ["Date", "Name", "Age", "Rank", "Classification", "Reason_Withdrawal", "Division", "Principal", "Remarks"]
    st.dataframe(df_curr[printable_cols], use_container_width=True)
    st.caption("Use browser print (Ctrl+P / Cmd+P) to print this table.")
