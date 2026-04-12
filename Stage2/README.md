# Project Charter: Rafdi (B2B Fractional Warehousing)

## 1. Project Objectives

### Purpose:
To develop a web-based B2B marketplace that connects warehouse owners who have unused shelf space with startups and SMEs that require fractional, specialized storage (e.g., chilled, frozen, or ambient) without the financial burden of renting an entire facility.

### SMART Objectives:
• Specific & Measurable: Develop and deploy a fully functional MVP with at least 3 core features (User Authentication, Listing Creation, and Search/Filter mechanism) within the allocated 4-week timeframe.  
• Achievable & Realistic: Successfully register a pilot database of at least 5 mock warehouse listings strictly focused on the Riyadh and Al-Kharj regions to validate the platform's search and filter logic.  
• Time-Bound: Complete end-to-end integration of all vertical features and finalize deployment by May 25.  

---

## 2. Stakeholders and Team Roles

### Stakeholders:
• Internal: The development team (Yasir, Nawaf, Mohammed, Owen), Holberton Instructors, and Peers.  
• External (Target Audience): Mock Warehouse Owners (Supply) and Mock Startup Owners (Demand).  

### Team Roles & Responsibilities (Vertical Feature Ownership):
To ensure efficient development and accommodate the team's tight schedule, we are adopting a Vertical Slicing approach. Each member is a Full-Stack Developer responsible for a specific feature from the database layer to the user interface:

• TBD (Project Manager & Full-Stack):  
o Feature Ownership: User Authentication (Login/Register) & User Profile Management.  
o Admin Role: Coordinates weekly tasks, ensures milestones are met, and oversees the final integration.  

• TBD (UI/UX Lead & Full-Stack):  
o Feature Ownership: Warehouse Listings (Create, Read, Update, Delete shelves/spaces).  
o Admin Role: Ensures consistent design across all features using standard CSS/Bootstrap templates to save time.  

• TBD (Backend Lead & Full-Stack):  
o Feature Ownership: Search engine and Filtering logic (filtering listings by temperature type, location, and dimensions).  
o Admin Role: Defines the API endpoints structure and ensures smooth data flow between front-end and back-end.  

• TBD (DevOps/QA & Full-Stack):  
o Feature Ownership: Booking/Request System (allowing a startup to send a request to a warehouse owner).  
o Admin Role: Manages the MySQL database schema creation, server deployment, and final application testing.  

---

## 3. Define Scope

To ensure the project is delivered on time while meeting all Holberton MVP requirements, the scope is strictly limited to core functionalities.

### In-Scope (Core Deliverables):
• User registration and authentication system (Owner vs. Renter).  
• Dashboard for warehouse owners to add shelf details (Dimensions, Temperature: Ambient/Chilled/Frozen, Location).  
• Search and filter functionality for renters to find spaces specifically in Riyadh and Al-Kharj.  
• A basic booking request system (sending a reservation interest).  

### Out-of-Scope (Explicitly Excluded):
• Actual payment gateways (transactions will be assumed or mocked).  
• Integration with third-party shipping and logistics APIs.  
• Real-time IoT temperature tracking of shelves.  
• Complex chat systems between users (communication will be handled via basic email or status updates).  

---

## 4. Identify Risks

| Risk Description | Impact | Mitigation Strategy |
|----------------|--------|--------------------|
| Strict Time Constraints: Team members have busy personal schedules, risking incomplete features. | High | Adhere strictly to the "In-Scope" features. Use vertical slicing so members can work independently without blocking others. |
| Integration Conflicts: Merging code from 4 different vertical slices might cause system breaks. | Medium | Define clear API contracts and the MySQL database schema during Week 1 before any coding begins. Conduct weekly merge sessions. |
| Deployment Issues: Configuring the production server (Nginx/Gunicorn/Flask) might take longer than expected. | Medium | Assign the DevOps role (Owen) to set up a basic "Hello World" deployment pipeline in the first week, rather than waiting until the end. |

---

## 5. High-Level Plan

The project lifecycle is planned from April 26 to May 25, structured into the following milestones:

• Week 1 (April 26 - May 2): Foundations & Design  
o Complete Stage 1 & Stage 2 (Idea & Project Charter).  
o Design the Database Schema (MySQL) and define API routes.  
o Set up the GitHub repository and base HTML/CSS templates.  

• Week 2 (May 3 - May 9): Core Logic Development (Stage 3 & 4 Start)  
o Finalize Technical Documentation.  
o Yasir completes Authentication logic.  
o Nawaf completes Listing creation logic.  

• Week 3 (May 10 - May 16): Feature Expansion & MVP Development  
o Mohammed finalizes Search & Filter algorithms.  
o Owen completes the Booking request functionality.  
o Begin integrating front-end forms with back-end APIs.  

• Week 4 (May 17 - May 25): Testing, Deployment, and Closure (Stage 5)  
o Merge all vertical slices and resolve conflicts.  
o Deploy the web application to the live server.  
o Perform end-to-end testing, record the project demonstration video, and submit the final MVP.