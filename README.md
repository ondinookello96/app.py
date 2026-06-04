import streamlit as st
import re
import json

# --- PAGE CONFIG ---
st.set_page_config(page_title="Ujima Agent Pride", page_icon="🦁", layout="wide")
st.title("🦁 Ujima Agent Pride: Live Orchestration Prototype")
st.markdown("Simulate the synchronized ambush of the Scout, Guardian, and Hunter agents.")

# --- TOOLS & GUARD RAILS ---
class DignityFilter:
    BANNED_WORDS = ["unreliable", "risky", "poor", "bad credit", "irresponsible"]
    @classmethod
    def check(cls, message):
        for word in cls.BANNED_WORDS:
            if re.search(rf'\b{word}\b', message, re.IGNORECASE):
                return False
        return True

# --- UI INPUTS ---
st.sidebar.header("📝 Loan Application Data")
name = st.sidebar.text_input("Applicant Name", "Grace")
age = st.sidebar.slider("Age", 18, 80, 42)
amount = st.sidebar.slider("Loan Amount (KES)", 1000, 100000, 28000, 1000)
children_under_5 = st.sidebar.slider("Children Under 5", 0, 5, 1)
occupation = st.sidebar.selectbox("Occupation", ["Maize Farmer", "Market Vendor", "Formal Employee", "Shea Butter Trader"])
sms_message = st.sidebar.text_area("Member SMS/Status", "I have no money for school fees next month.")

# --- AGENT LOGIC ---
if st.sidebar.button("🚀 Run Agent Pride Simulation", type="primary"):
    
    col1, col2, col3 = st.columns(3)
    
    # 1. SCOUT AGENT
    with col1:
        st.subheader("🦁 Scout Agent")
        stress_keywords = ["school fees", "loan shark", "shylock", "no money", "desperate"]
        is_stressed = any(kw in sms_message.lower() for kw in stress_keywords)
        
        if is_stressed:
            st.warning("⚠️ Stress Signal Detected!")
            st.info("Action: HUNT Trigger -> Pass to Guardian")
        else:
            st.success("No distress. Continuing literacy loop.")
            
    # 2. GUARDIAN AGENT
    with col2:
        st.subheader("🛡️ Guardian Agent")
        risk_flags = []
        if occupation in ["Market Vendor", "Shea Butter Trader"]:
            risk_flags.append("informal_sector") # Historical bias flag
            
        # PRIDE LOOP PAUSE POINT
        if amount > 15000 or children_under_5 >= 2:
            st.error(f"🛑 PRIDE PAUSE POINT TRIGGERED")
            st.write(f"Reason: Amount > 15k OR Vulnerable Profile.")
            decision = "ESCALATE"
        elif len(risk_flags) >= 3:
            harsh_msg = "Denied: You are a risky borrower."
            if not DignityFilter.check(harsh_msg):
                st.warning("🚨 Dignity Filter Blocked Harsh Message. Rewriting...")
            decision = "DENY"
        else:
            decision = "APPROVE"
            
        st.metric("Decision", decision)

    # 3. HUNTER AGENT
    with col3:
        st.subheader("🏹 Hunter Agent")
        if decision == "ESCALATE":
            st.success("🤝 Human Handoff Initiated")
            st.json({
                "Officer_Alert": "High Priority",
                "Applicant": f"{name}, {age}, {occupation}",
                "Context": f"Amount: {amount}, Kids <5: {children_under_5}",
                "SLA": "15 Minutes"
            })
        else:
            st.info(f"Auto-Processed: {decision}. No human needed.")

    # --- AUDIT TRAIL ---
    st.markdown("---")
    st.subheader("🔒 Immutable TRAIL Log (AWS Cape Town)")
    audit_log = {
        "timestamp": "2026-06-04T10:00:00Z",
        "scout_signal": is_stressed,
        "guardian_decision": decision,
        "dignity_filter_passed": True,
        "counterfactual": f"If income +20%, decision = APPROVE"
    }
    st.code(json.dumps(audit_log, indent=2), language="json")
