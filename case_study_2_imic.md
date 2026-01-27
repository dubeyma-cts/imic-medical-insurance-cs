**Real World Project**

**Indian Medical Insurance Corporation (IMIC)**

Case Study

1.  Introduction
    1.  Background

The purpose of this case study is to provide a real world application to Software Engineer Trainees, which they can develop by going through all the stages of a Software Development Life Cycle (SDLC).

This is to achieve a reduced time to production of Trainees so that they are “project ready” by the time they are put onto projects.

1.  Overview

The case study is for an application for a fictitious medical insurance company called **Indian Medical Insurance Corporation (IMIC)**. There are three stakeholders of this system: The Insurance Company, Policy holder & Insurance agent. The application used by Policy holder & the Insurance agent will be an **_internet_** application. The application used by the Insurance Company will be **_backend_** application.

Apart from developing the application, Trainees are also expected to analyze the requirements and apply OOAD principles to come up with a proper design. Trainees are expected to follow Cap Gemini coding standards and adhere to best practices and coding principles. Trainees will also have to deploy their application so that it can be tested. For this purpose, they will have to create an installation pack, which will deploy their application, with minimal manual intervention.

Apart from the core functionality, the SET must also take into consideration important issues like security, ease of use, familiar look-and-feel across the application, easy navigation and performance.

1.  Requirements
    1.  Buying a Policy

The customer can buy a medical insurance policy through an insurance agent. The customer will have to submit an application to the agent to buy a policy. Each policy can have 1 primary member & multiple dependents, where the customer is the primary member or the policy holder & his/her family members are dependents. Along with the application, the customer will also have to submit other supporting documents like medical diagnostic reports & age proof for each & every member. Each policy will have a minimum amount associated with it; it is called policy cover. Depending on the policy cover, age & other such factors, the premium is charged to the customer. Higher the policy cover amount, higher is the premium. The premium is charged for the primary member as well as his/her dependents.

After receiving all the necessary details from the customer, the agent will logon to the Agent section of the site, create the customer & enter all the details of the proposed policy. After entering the details, the agent will submit all the documents received from the customer to the insurance company. The insurance company will check the authenticity of all the documents & either approve or reject the policy application. The application is either accepted or rejected for all the members (primary + dependents) of the policy i.e. if the policy application is for 6 members & even if only one member is rejected, the entire policy application gets rejected. The application can get rejected due to various factors like age, invalid medical reports, bouncing of policy amount cheque etc.

Once the application is accepted, the insurance company will send the policy to the policy holder i.e. the customer. The policy holder will also get a letter containing the username & password for the site. The policy holder will be able to log on to the site only after that. After logging on to the site, the policy holder will be able to view his/her personal details as well as policy details. The status of the policy is ‘Active’. The information displayed to the policy holder is read-only i.e. the policy holder will not be allowed to edit any information.

1.  Claims

A policy holder will be eligible to apply for a claim immediately after the policy is sent to him/her by the insurance company. The claim can be for the primary member or even his/her dependents. The policy holder will submit the claim along with other documents like hospital bills, diagnostic reports etc. to the agent. The agent will enter all the details of the claim into the system & send all the documents to the insurance company. The policy holder will be able to view the claim status by logging on to the system.  The insurance company will validate all the submitted documents & either accept or reject the claim. If the claim is accepted, a cheque of the claimed amount will be sent to the policy holder; cheque details will be entered by the insurance company against the claim in the system.

By default, the claim status will be ‘Sent for approval’; if the claim is accepted, the status will be changed to ‘Accepted’ & cheque details will be entered; if it is rejected, the status will be changed to ‘Rejected’ with the reason for rejection.  

1.  Internet Site Details
    1.  Policy Holder Section

This section will be used by the Policy holder. When the policy holder types the URL, by default, the home page will be displayed in the browser. After logging on to the system, Policy Holder home page will be displayed to the policy holder.

1.  Home Page

The Home page will have basic features like Header, General Work area & Login area.

*   Header area: This area will display the company logo.
*   General Work area: This area will have sections like General insurance news, Frequently Asked Questions (FAQs).
*   Login area: The policy holder will be able to logon to the site by entering the username & the password in this area.

1.  Policy Holder Home Page

This page will be displayed after the policy holder logs on to the site. This page will have a menu area as well as a work area. On selection of a menu item, appropriate page will be displayed in the work area.

Following menu options will be displayed to the policy holder: Personal details, Policy details, Claims details.

*   Personal Details

All the personal details of the policy holder will be displayed on the page. Personal details will be entered by the agent & are view-only to the policy holder.

*   Policy Details

Details of the policy will be displayed on this page. Policy status will also be displayed on this page. Policy details will be entered by the agent & are view-only to the policy holder.

*   Claims Details

Details of all the claims submitted by the policy holder will be displayed on this page. Status of claims will also be displayed. Claims details will be entered by the agent & are view-only to the policy holder.

1.  Agent Section

This section will be used by the Agent. When the agent types the URL, by default, the home page will be displayed in the browser. After logging on to the system, Agent Home Page will be displayed to the agent.

1.  Home Page

The Home page will have basic features like Header, General Work area & Login area.

*   Header area: This area will display the company logo.
*   General Work area: This area will have sections like General insurance news, Frequently Asked Questions (FAQs). This page is common for a policy holder & an agent
*   Login area: The agent will be able to logon to the site by entering the username & the password in this area.

1.  Agent Home Page

This page will be displayed after the agent logs on to the site. This page will have a menu area as well as a work area. On selection of a menu item, appropriate page will be displayed in the work area.

Following menu options will be displayed to the agent: New Policy Holder, Claims Management, and Policy Holder Communication.

*   New Policy Holder

The agent will use this section to enter details of a new policy holder. Apart from the personal information of the policy holder, the agent will also be able to enter information of the policy bought by the policy holder.

*   Policy Status

The Agent will be able to see the status of all the policies submitted by the customers

*   Claims Management

The agent will use this section to enter details of claims submitted by policy holders.

1.  Intranet Site Details
    1.  Company Section

This section will be used by the Insurance Company. When the company staff types the URL, by default, the home page will be displayed in the browser. After logging on to the system, Company Home Page will be displayed to the policy holder.

1.  Home Page

The Home page will have basic features like Header, General Work area & Login area.

*   Header area: This area will display the company logo.
*   General Work area: This area will have important information in static form.
*   Login area: The insurance company staff will be able to logon to the site by entering the username & the password in this area.

1.  Company Home Page

This page will be displayed after the company staff logs on to the site. This page will have a menu area as well as a work area. On selection of a menu item, appropriate page will be displayed in the work area.

Following menu options will be displayed to the agent: Approve/Disapprove Policy and Claims Management.

*   Approve/Disapprove Policy

The Insurance Company will see a list of all the applications submitted by the agents on behalf of policy holders. The company will either approve or reject an application based on the documents submitted by the policy holder.

*   Claims Management

The company will see a list of all the claims submitted by agents on behalf of the policy holders. The company will either approve or reject a claim based on the documents submitted by the policy holder. The policy holder will be able to see the status of his/her claims in his/her section of the site.

1.  Look-and-Feel

All pages must have the same look-and-feel with exactly the same header, footer and navigation panel on the left.

Users must be able to go to any screen with minimum mouse-clicks.

1.  Security

After three consecutive invalid logon attempts, a user’s login account should be locked. This login account can only be re-activated manually.

1.  Deliverables

Trainees are expected to deliver the following SDLC artifacts in the form of **_documents_**:

*   Use Case Documents
*   Functional Specifications
*   Test Plan
*   Test Cases

They are also expected to deliver the following SDLC artifacts on **_paper_**:

*   Activity Diagrams
*   Use Case Diagrams
*   Sequence Diagrams

New Projects

Use Cases and Use Diagrams

Component Diagrams

Layered Diagrams

Cross Cutting Concerns

Multitier Diagrams

Architectures and their Tradeoff Analysis

*   Performance and Reliability Tradeoff

ER Diagrams