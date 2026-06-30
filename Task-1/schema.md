# Google Tag Manager (GTM) Event Tracking Schema
## OrthoNow – Healthcare Website Analytics Implementation

**Developer Assignment – Task 1**

---

# 1. Project Overview

## Objective

OrthoNow currently has limited analytics implementation with only page views being tracked in Google Analytics 4 (GA4). As a result, the marketing team has very little visibility into how users interact with the website, where potential patients abandon the booking process, or which marketing campaigns generate actual consultations.

The objective of this implementation is to design a scalable event tracking architecture using Google Tag Manager (GTM) that captures all meaningful user interactions across the website. The collected data will support Google Analytics 4 reporting, Google Ads conversion tracking, remarketing audience creation, and future CRM integrations.

This tracking strategy focuses on measuring patient engagement, monitoring the complete appointment booking funnel, tracking communication preferences such as phone calls and WhatsApp clicks, analysing content engagement, and providing actionable insights that help improve conversion rates.

---

# 2. Tracking Objectives

The tracking implementation has the following objectives:

- Measure all important user interactions across the website.
- Track every step of the appointment booking process.
- Identify funnel drop-offs between booking steps.
- Measure clinic-specific user engagement.
- Track consultation form submissions.
- Measure phone call interactions.
- Track WhatsApp enquiries.
- Track patient guide downloads.
- Measure blog engagement using scroll depth.
- Create remarketing audiences inside GA4.
- Import qualified conversions into Google Ads.
- Build a scalable analytics implementation that can support future website enhancements.

---

# 3. Event Tracking Flow

```

Website Visitor

↓

Performs an Action

↓

Frontend JavaScript fires

window.dataLayer.push(...)

↓

Google Tag Manager detects event

↓

GA4 Event Tag fires

↓

Google Analytics 4 stores event

↓

Google Ads receives conversion

↓

Marketing Team analyses reports

```

This architecture separates business logic from analytics implementation. The frontend application is responsible for notifying GTM whenever important user actions occur. GTM listens for these events and forwards the required information to GA4 and Google Ads.

---

# 4. GTM Event Schema

| Event Name | Trigger Type | Event Parameters | Purpose | GA4 Report / Audience |
|------------|--------------|-----------------|---------|----------------------|
| appointment_booking_started | Custom Event | clinic_location, specialty, page_url | Booking started | Funnel Exploration |
| booking_step_complete | Custom Event | step_number, step_name, clinic_location | Funnel progression | Funnel Exploration |
| booking_completed | Custom Event | booking_id, clinic_location, specialty | Successful appointment | Conversion Report / Google Ads |
| consultation_form_view | Element Visibility | form_name, page_url, campaign | Form impressions | Engagement Report |
| consultation_form_submitted | Form Submission | clinic_location, device_type, lead_source | Lead generation | Conversion Report |
| call_button_click | Click Trigger | phone_number, page_name, clinic_location | Phone enquiries | Engagement |
| whatsapp_click | Link Click | page_name, clinic_location, device_type | WhatsApp enquiries | Audience Creation |
| patient_guide_form_started | Form Interaction | guide_name, page_url, campaign | Lead funnel | Engagement |
| patient_guide_download | Form Submission | guide_name, file_type, page_url | Lead conversion | Conversion Report |
| clinic_page_view | Page View | clinic_name, city, page_title | Clinic popularity | Pages & Screens Report |
| blog_scroll_25 | Scroll Trigger | article_title, category, scroll_percentage | Content engagement | Engagement |
| blog_scroll_50 | Scroll Trigger | article_title, category, scroll_percentage | Content engagement | Engagement |
| blog_scroll_75 | Scroll Trigger | article_title, category, scroll_percentage | Content engagement | Engagement |
| blog_scroll_100 | Scroll Trigger | article_title, category, scroll_percentage | Highly engaged readers | Audience |
| session_start | Page View | landing_page, source, medium | Traffic acquisition | Acquisition Report |

---

# 5. Booking Funnel Tracking Strategy

The appointment booking process consists of three user interactions.

Step 1

Select Clinic Location and Medical Specialty

↓

Step 2

Enter Patient Details

(Name, Phone Number and Preferred Appointment Date)

↓

Step 3

Confirm Appointment

↓

Appointment Successfully Booked

Each completed step generates a custom event through the dataLayer. These events are captured by GTM and forwarded to GA4.

This allows Funnel Exploration inside GA4 to calculate:

- Users entering the funnel
- Users completing each step
- Step-wise conversion rate
- Step-wise abandonment rate
- Overall booking completion rate

This information helps identify friction points within the booking process.

---

# 6. dataLayer Implementation

Since the booking flow is a JavaScript-based multi-step form, GTM cannot automatically detect movement between form steps.

The frontend developer must explicitly fire a custom dataLayer event whenever a user successfully completes a booking step.

## Step 1

```javascript
window.dataLayer = window.dataLayer || [];

window.dataLayer.push({
  event: "booking_step_complete",
  step_number: 1,
  step_name: "location_specialty_selected",
  clinic_location: "Indiranagar",
  specialty: "Orthopaedics"
});
```

## Step 2

```javascript
window.dataLayer.push({
  event: "booking_step_complete",
  step_number: 2,
  step_name: "patient_details_entered",
  clinic_location: "Indiranagar",
  specialty: "Orthopaedics",
  preferred_date: "2026-07-05"
});
```

## Step 3

```javascript
window.dataLayer.push({
  event: "booking_completed",
  booking_id: "BK100245",
  clinic_location: "Indiranagar",
  specialty: "Orthopaedics",
  booking_date: "2026-07-05"
});
```

## Implementation Note

The example values shown in the dataLayer payloads are sample values used to demonstrate the event structure. In the actual implementation, the frontend developer will populate these values dynamically from the user's input and the backend response. Google Tag Manager listens for these custom events and forwards them to Google Analytics 4 and Google Ads.

# Google Ads Conversion Action

The conversion action that should be imported into Google Ads is `consultation_form_submitted`.

This event represents a qualified lead because the user has successfully completed and submitted the consultation request form. Optimizing campaigns toward this conversion helps Google Ads focus on users who are more likely to become actual patients rather than users who only interact with the landing page.