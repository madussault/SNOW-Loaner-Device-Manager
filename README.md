# 💻 Loaner Device Manager (ServiceNow Scoped App)

## 📋 Overview
The **Loaner Device Manager** is a custom-built ServiceNow application designed to streamline the process of temporarily borrowing IT equipment (laptops, projectors, mobile devices). It handles the full lifecycle of a hardware loan, from the initial user request via the Service Portal to manager approval, IT fulfillment, and automated overdue tracking. 

<p align="center">
  <img width="500" alt="image" src="https://github.com/user-attachments/assets/cbc18e8c-b6a2-4bc9-8765-6f1bababcaca" />
</p>

*Note: This is a fictional application developed as a portfolio project to demonstrate end-to-end ServiceNow development capabilities.*

## 🏗️ Architecture & Key Features

### 1. Data Model & Integrity
* **Custom Table:** Built a dedicated Loaner Request table extending the Task table. This approach was chosen to automatically inherit core system fields—such as assigned_to, opened_by, work_notes, approval, and state.
* **Data Integrity:** Implemented Data Policies and Dictionary Overrides to enforce mandatory fields dynamically based on the state of the request (e.g., ensuring devices and dates are required before deployment).
* **State Management**: Built a Business Rule to prevent illogical state regressions (e.g., preventing a "Deployed" state from moving backwards to "Requested").
* **Reference Fields & Dot-Walking:** Connected to the standard alm_asset (Hardware) and sys_user tables, using dot-walking to pull related data (like asset tags and manager names) directly onto the form as read-only text.

### 2. User Experience (UI/UX)
* **Service Portal Interface:** Developed a user-friendly **Record Producer** accessible via an Icon Link on the Service Portal, allowing end-users to submit requests seamlessly.
* **Progressive Disclosure:** Utilized **Catalog UI Policies** (Portal) and standard **UI Policies** (Backend) to hide schedule fields until prerequisite data is entered, keeping the form clean.
* **Client-Side Validation:** Created an `onSubmit` **Client Script** that validates date logic (ensuring the return date cannot be before the pickup date) and returns an error messages.
* **Custom UI Actions:** Built form buttons enabling users to cancel their requests before the device is "deployed".

### 3. Workflow Automation (Flow Designer)
* **Dynamic Approvals:** Automatically routes the request to the specific user's manager for approval.
* **Automated Record Updates:** Automatically updates the request's Approval field and injects a Work Note(mandatory) if the request is rejected.

### 4. Security (ACLs)
* **Requester:** Can create requests, view their own active tickets (`Opened by = Me`), and edit specific fields (like dates) *only* if the state is still "Requested".
* **State-Driven Security:** Fields dynamically lock down (Read-Only) for all requesters once a ticket progresses past the Approval phase.
* **IT Fulfiller / Admin:** Granted full read/write access to manage the requests. Only Admin can delete.

### 5. Scheduled Jobs & Event-Driven Notifications
* **Nightly Automation & Events:** Built a server-side Scheduled Script Execution that runs daily at midnight. It uses GlideRecord queries to identify "deployed" devices past their End Date and fires a custom event (x_1883746_loaner_0.loaner.overdue) for each one.
* **Email Notifications:** Configured a notification that listens for the event and sends a dynamically generated email to the requester, asking them to return their device.

### 6. IT Staff Dashboard (Platform Analytics)
Created a centralized reporting dashboard for IT Asset Managers featuring:
* **Single Score Reports:** Tracking real-time "Overdue Devices" and "Active Deployments."
* **Data Visualization:** A Pie Chart breaking down all historical requests by their current State.

## Backend form for the "loaner request" table:
<img width="1437" height="817" alt="image" src="https://github.com/user-attachments/assets/3e8d4506-3947-4d3d-b07d-4b3573fe286a" />

## Loan Approval Flow:
<img width="500" alt="image" src="https://github.com/user-attachments/assets/75e9a8af-5051-46d6-807b-b144a726afd4" />

## Dashboard:
<img width="1909" height="901" alt="image" src="https://github.com/user-attachments/assets/d72b526d-7207-4e8b-b58b-20c0f9065621" />

## Code Highlights (Prevent State Regression)
<img width="550" alt="image" src="https://github.com/user-attachments/assets/698f94d6-dbbd-4bc8-b6cc-e5f81c4e1529" />



