# CRM & Marketing Integration Design

## OrthoNow Healthcare Website

Developer Assignment – Task 3

## Project Overview

When a patient submits the consultation form on the OrthoNow landing page, the submitted information should be processed through a backend service before being forwarded to external systems. The backend acts as the central integration layer responsible for validating the submitted data, communicating with third-party services, handling failures, and ensuring that patient information is stored securely.

The integration has four primary objectives:

- Store every consultation request in HubSpot CRM.
- Send an automated WhatsApp confirmation message through Karix.
- Record successful lead conversions inside Google Ads.
- Maintain reliable logging and error handling for all external API requests.

---

# System Architecture Flow

```
Patient

↓

Landing Page Form

↓

Frontend Validation (JavaScript)

↓

Backend API

↓

+------------------------+
| Store Lead in HubSpot  |
+------------------------+

↓

+------------------------------+
| Send WhatsApp via Karix API  |
+------------------------------+

↓

+--------------------------------------+
| Send Conversion to Google Ads / GA4  |
+--------------------------------------+

↓

Return Success Response

↓

Display Thank You Message
```
The integration begins when a patient submits the consultation form on the landing page. The frontend performs basic validation to ensure that the required fields are completed before sending the data to the backend API.

The backend validates the request once again and becomes responsible for all third-party integrations. It first stores the patient information in HubSpot CRM to ensure that every enquiry is recorded. After a successful CRM insertion, the backend triggers the Karix WhatsApp API to send an appointment acknowledgement message to the patient. Finally, a successful lead conversion event is forwarded to Google Ads and Google Analytics 4 for campaign attribution and marketing performance reporting.

Once all required operations are completed successfully, the backend returns a success response to the frontend, allowing the website to display the thank-you message to the user.

---

# HubSpot CRM Integration

## Purpose

HubSpot CRM is used as the primary system for storing and managing patient enquiries. Every consultation request submitted through the landing page is recorded as a new contact, allowing the clinic staff to track leads, schedule follow-up calls, and monitor the consultation pipeline.

## Data Stored

The following information is stored in HubSpot CRM:

| Field | Description |
|-------|-------------|
| Full Name | Patient's name entered in the consultation form |
| Phone Number | Patient's contact number |
| Lead Source | Landing Page |
| Clinic Location | Selected clinic or default landing page location |
| Submission Time | Timestamp of form submission |
| Lead Status | New Lead |

## Integration Flow

1. The frontend sends the consultation request to the backend API.
2. The backend validates the submitted data.
3. The backend authenticates with the HubSpot API.
4. A new contact is created inside HubSpot CRM.
5. HubSpot returns a Contact ID.
6. The backend stores the Contact ID for future reference.

---

# Karix WhatsApp Integration

## Purpose

After a consultation request is successfully stored in HubSpot CRM, the patient should immediately receive a WhatsApp confirmation message. This reassures the patient that the request has been received and reduces uncertainty while waiting for the clinic to contact them.

## Message Flow

1. The backend receives confirmation that the HubSpot contact has been created successfully.
2. The backend sends a request to the Karix WhatsApp API.
3. Karix delivers a confirmation message to the patient's registered WhatsApp number.
4. The backend records whether the message was successfully delivered or if an error occurred.

## Sample WhatsApp Message

Hello {{Patient Name}},

Thank you for contacting OrthoNow Healthcare.

We have successfully received your consultation request. Our team will contact you shortly to confirm your appointment.

Thank you,
OrthoNow Healthcare

---

# Google Ads & Google Analytics 4 Integration

## Purpose

Google Analytics 4 (GA4) is used to measure user interactions across the website, while Google Ads uses conversion data to evaluate advertising campaign performance. Every successful consultation request is tracked as a conversion event, allowing the marketing team to understand which campaigns generate qualified patient enquiries.

## Event Flow

1. The frontend pushes a custom event to the dataLayer after a successful form submission.
2. Google Tag Manager detects the custom event.
3. GTM triggers a GA4 Event Tag.
4. Google Analytics 4 records the consultation submission event.
5. The conversion is imported into Google Ads for campaign optimization and reporting.

## Event Name

consultation_form_submitted

## Event Parameters

| Parameter | Description |
|-----------|-------------|
| clinic_location | Clinic selected or landing page location |
| device_type | Desktop or Mobile |
| lead_source | Landing Page |
| page_url | URL where the form was submitted |
| submission_time | Timestamp of the submission |

## Benefits

- Measure marketing campaign performance.
- Track lead generation conversions.
- Build remarketing audiences.
- Analyze consultation funnel performance.
- Optimize advertising spend using conversion data.

---

# Error Handling

Reliable error handling ensures that patient enquiries are not lost even if one or more external services become temporarily unavailable.

## Error Handling Strategy

| Scenario | Action Taken |
|----------|--------------|
| HubSpot API unavailable | Store the request in a retry queue and attempt again later. |
| Karix API failure | Log the failure and retry sending the WhatsApp message. |
| Google Ads API unavailable | Log the conversion request and retry asynchronously. |
| Backend validation failure | Return an appropriate error message to the frontend. |

## Retry Strategy

- Retry failed API requests using exponential backoff.
- Record every failed request in application logs.
- Notify administrators if repeated failures occur.

---

# Security Considerations

The following security practices should be followed during implementation:

- All communication should occur over HTTPS.
- API credentials should be stored securely using environment variables.
- Patient information should never be exposed in frontend JavaScript.
- Personally identifiable information (PII) should not be sent to Google Analytics 4.
- Backend input validation should be performed before processing requests.
- Rate limiting should be implemented to prevent spam submissions.

---

# Monitoring & Logging

Monitoring helps identify failures quickly and ensures that patient enquiries are processed successfully.

The backend should log:

- Consultation request received.
- HubSpot API response.
- Karix API response.
- Google Ads conversion status.
- API errors and retry attempts.
- Successful completion of the entire integration workflow.

Monitoring dashboards can be used to track API success rates, response times, failed requests, and overall system health.


---

# Conclusion

This proposed architecture separates frontend analytics from backend business logic while ensuring reliable CRM integration, marketing attribution, and patient communication. The design is scalable, fault tolerant, and supports future integrations without requiring significant frontend changes.