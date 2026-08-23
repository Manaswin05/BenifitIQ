# Product Requirements Document (PRD) - BenefitIQ

## 1. Product Vision
BenefitIQ aims to eliminate friction in tracking disparate credit card rewards, thereby reducing voluntary cardholder churn and maximizing portfolio ROI. It serves as an AI-powered Benefit Intelligence Platform that ensures cardholders never miss a benefit.

## 2. Target Audience
- **Cardholders:** Looking to maximize the value derived from their credit card benefits seamlessly.
- **Credit Card Issuers/Banks:** Aiming to increase card usage, improve customer loyalty, and track campaign ROI.

## 3. Problem Statement
The "Underutilization Gap" results in approximately $1,200 per card member per year left unclaimed. Friction in manually tracking various, complex reward structures leads to decreased engagement and voluntary churn.

## 4. Key Metrics & Projected Impact
- **Targeted Engagement Lift:** +18% to 25%
- **Targeted Churn Reduction:** 12% to 15%
- **Per-Member Value Recaptured:** $1,200

## 5. Core Features & Requirements

### 5.1 BenefitIQ Engine (Core Backend)
- **Classify:** Real-time processing of transaction streams to identify merchants and match them against complex benefit rules.
- **Predict:** ML-driven gap prediction using Spring AI to quantify unclaimed dollar value based on:
  - Spend Recency
  - Category Frequency
  - Historical Redemption
- **Nudge:** A rules/agent engine that processes context-aware trigger logic.
  - *Example:* IF [Dining Benefit > $50] AND [User Location = Restaurant District] AND [Expiry < 30 Days] THEN Trigger Push API.

### 5.2 Real-Time Benefit Observability Dashboard (Frontend)
- **Benefit Category Heatmaps:** Visual representations of popular and underutilized benefit categories.
- **Cohort-Level Gap Scores:** Aggregated data showcasing missed opportunities by demographic or card type.
- **Campaign ROI Tracking:** Tracking the effectiveness of triggered nudges on subsequent spending behavior.

## 6. Non-Functional Requirements
- **Scalability:** Must handle high-throughput real-time mock transaction payloads.
- **Performance:** Low latency for the Context-Aware Trigger Logic to push notifications while the user is actively transacting or in-location.
- **Extensibility:** The AI agent layer (Spring AI) should be modularized to easily swap or upgrade underlying LLMs/models.
